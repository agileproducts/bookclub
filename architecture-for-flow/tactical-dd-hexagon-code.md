# Implementing tactical DDD using a hexagonal architecture

We're using TypeScript for these examples rather than the book's Java, because it lets us show the same ideas with a lot less ceremony. If you've never written a line of code, don't worry - each snippet is followed by a plain-English walkthrough. The goal isn't to teach you TypeScript, it's to make the *shapes* of the code recognisable so the concepts from our other explainer feel concrete.

## Objects and classes

A **class** is a template for creating things that have some data and some behaviour attached to them. An **object** is one specific thing built from that template.

```typescript
class Author {
  name: string;
  affiliation: string;

  constructor(name: string, affiliation: string) {
    this.name = name;
    this.affiliation = affiliation;
  }

  citation(): string {
    return `${this.name} (${this.affiliation})`;
  }
}

const jane = new Author("Jane Smith", "University of Bristol");
console.log(jane.citation()); // "Jane Smith (University of Bristol)"
```

Walking through this:
- `class Author { ... }` is the template - it says "every author has a `name` and an `affiliation`, and can produce a `citation`".
- `name: string` and `affiliation: string` are the **data** the class holds. The `: string` part just says "this must be text".
- `constructor(...)` is how you create a new author - you must supply a name and affiliation up front.
- `citation()` is a piece of **behaviour** attached to the class - something an author object can *do*, using its own data.
- `const jane = new Author(...)` creates one actual **object** - a specific author - from the `Author` template. `jane` isn't a description of all authors, it's one real instance.

This is exactly the `Author` value object we described in our building-blocks explainer, just written as code rather than prose.

## Interfaces

An **interface** describes a *shape* - what something must be able to do - without saying anything about how it's actually built. Nothing is created directly from an interface; instead, a class can promise to match one.

```typescript
interface Notifier {
  notifyDecision(submissionId: string, decision: string): void;
}
```

Read this as a contract: "anything claiming to be a `Notifier` must provide a `notifyDecision` function that takes a submission ID and a decision, and doesn't need to hand anything back." That's the entire promise - nothing here says *how* the notification happens.

Now here are two completely different classes, both satisfying that same promise:

```typescript
class EmailNotifier implements Notifier {
  notifyDecision(submissionId: string, decision: string): void {
    console.log(`Emailing author about ${submissionId}: ${decision}`);
    // real code here would call an email-sending service
  }
}

class FakeNotifier implements Notifier {
  sentMessages: string[] = [];

  notifyDecision(submissionId: string, decision: string): void {
    this.sentMessages.push(`${submissionId}: ${decision}`);
    // no real email is sent - useful for tests and rehearsals
  }
}
```

Both `EmailNotifier` and `FakeNotifier` say `implements Notifier`, which means TypeScript will check that they really do provide a working `notifyDecision` function. Beyond that one shared promise, they can be as different as you like internally - one sends a real email, the other just remembers what it was asked to do, for use in tests.

Anything elsewhere in the code that only knows about `Notifier` - not `EmailNotifier` or `FakeNotifier` specifically - can be handed *either* of these objects and will work identically, without knowing or caring which one it got.

## Why this distinction matters for what's coming next

This is the exact mechanism behind the "ports and adapters" idea from our other explainer:
- The **interface** (`Notifier`) is the **port** - a promise written in the office's own language, deciding nothing about the outside world.
- Each **class implementing it** (`EmailNotifier`, `FakeNotifier`) is an **adapter** - a specific way of fulfilling that promise, swappable at will.

In the next section we'll put these pieces together: a `Submission` class enforcing its own business rules, and a small piece of code that only ever talks to a `Notifier` interface - never knowing or caring whether it's really sending an email or just being rehearsed in a test.

## Putting it all together: recording an editor's decision

The book walks through its conference-booking example as four layers, from the outside in. We'll mirror that structure using our own domain and our own `Submission` aggregate from the building-blocks explainer - specifically the rule "a decision can't be recorded until at least two reviews are in".

The four layers, outside to inside:

1. **HTTP API** - a *driving* adapter. Something out in the world (a browser, another service) calls in.
2. **Submission application service** - orchestrates the use case. Doesn't contain business rules itself, just coordinates.
3. **`Submission` aggregate** - the domain model and aggregate root. This is where the real business rules live.
4. **Repository** - a *driven* adapter. The aggregate's data needs to be saved and loaded somewhere; this hides where and how.

Notice the direction of each arrow: the HTTP API *drives* the application into action (it's on the left of our hexagon diagram from the other doc); the repository is *driven by* the application to do something on its behalf (it's on the right). Same pattern, opposite direction.

### 1. The HTTP API (driving adapter)

```typescript
// A driving adapter: translates an incoming HTTP request into a call
// on the application service. It knows about HTTP; nothing further in
// does.

app.post("/submissions/:submissionId/decision", (req, res) => {
  const { submissionId } = req.params;
  const { decision } = req.body; // "accepted" or "rejected"

  try {
    submissionService.recordDecision(submissionId, decision);
    res.status(200).json({ submissionId, decision });
  } catch (err) {
    res.status(400).json({ error: (err as Error).message });
  }
});
```

Plain English: when a `POST` request arrives at this web address (an editor clicking "Accept" or "Reject" in some editorial tool), pull the decision out of it, and hand it to `submissionService` - the application service - to actually do the work. All this code knows about is HTTP requests and responses; it has no idea what rules govern whether a decision is actually allowed.

### 2. The Submission application service (orchestration)

```typescript
// Orchestrates the use case. Fetches the aggregate, asks it to do
// something, saves the result. Contains no business rules of its own.

class SubmissionService {
  constructor(
    private repository: SubmissionRepository,
    private notifier: Notifier, // the port from the previous section
  ) {}

  recordDecision(submissionId: string, decision: Decision): void {
    const submission = this.repository.findById(submissionId);
    if (!submission) {
      throw new Error(`No such submission: ${submissionId}`);
    }

    submission.recordDecision(decision);

    this.repository.save(submission);
    this.notifier.notifyDecision(submissionId, decision);
  }
}
```

Plain English: this is a coordinator, not a decision-maker. It doesn't know *why* a decision might be refused - it just: loads the right `Submission` aggregate, asks it to record the decision (handing off all the actual rule-checking), saves whatever the aggregate decided, then notifies the author via the `Notifier` port we met earlier. Note it depends on `SubmissionRepository` and `Notifier` - both interfaces/ports, not concrete technologies - so it has no idea where data is stored or how notifications actually get sent.

### 3. The `Submission` aggregate (the domain model)

```typescript
// The aggregate root. This is where business rules actually live and
// are enforced - nothing outside this class is allowed to bypass them.

class Submission {
  private reviews: Review[] = [];
  private status: "Under Review" | "Accepted" | "Rejected" = "Under Review";

  constructor(
    public readonly id: string,
    private readonly authors: Author[],
  ) {}

  addReview(review: Review): void {
    this.reviews.push(review);
  }

  recordDecision(decision: Decision): void {
    if (this.reviews.length < 2) {
      throw new Error("At least two reviews are required before a decision can be recorded");
    }
    if (this.status !== "Under Review") {
      throw new Error(`Cannot record a decision - submission is already ${this.status}`);
    }

    this.status = decision === "accepted" ? "Accepted" : "Rejected";
  }
}
```

Plain English: this is the part with actual opinions. It refuses to let a decision be recorded until at least two reviews exist, and refuses to let a submission be decided twice - rules that matter to editors and authors, not to computers. Crucially, this class knows nothing about HTTP, databases, or email. You could test all of these rules by creating a `Submission` object directly in a test, adding a couple of fake reviews, and calling `recordDecision` on it - no web server, database, or email system required.

### 4. The repository (driven adapter, and its port)

First, the port - the promise the application service depends on:

```typescript
// The port: a promise about what "storage" must be able to do for us,
// written entirely in the domain's own language.

interface SubmissionRepository {
  findById(id: string): Submission | undefined;
  save(submission: Submission): void;
}
```

Then a concrete adapter fulfilling that promise - here shown as a simple in-memory store, standing in for "some actual database" (Mongo, Postgres, whatever we chose, doesn't matter):

```typescript
// A driven adapter: one specific way of fulfilling the storage promise.
// Swap this class for a real database adapter and nothing above it
// needs to change at all.

class InMemorySubmissionRepository implements SubmissionRepository {
  private store = new Map<string, Submission>();

  findById(id: string): Submission | undefined {
    return this.store.get(id);
  }

  save(submission: Submission): void {
    this.store.set(submission.id, submission);
  }
}
```

Plain English: the application service only ever talks to `SubmissionRepository` - the interface. Whether `findById` and `save` are backed by an in-memory map (as here, handy for tests), a real database, or even a call to another team's API, is invisible to everything else in the system. This is precisely the same relationship as `Notifier`/`EmailNotifier`/`FakeNotifier` from the previous section, just applied to storage instead of notifications.

### Tracing one request through all four layers

Putting it together, a single incoming request flows like this:

1. An editor clicks "Accept" on `MS-2024-00812` → the **HTTP API** adapter parses the request and calls `submissionService.recordDecision(...)`.
2. The **application service** asks the repository port to `findById("MS-2024-00812")`, gets back a `Submission` aggregate, and calls `recordDecision` on it.
3. The **aggregate** checks that at least two reviews exist and that it hasn't already been decided, then updates its own status - or throws an error if a rule is violated.
4. The **application service** asks the repository port to `save` the updated aggregate, then asks the `Notifier` port to tell the author - and hands a response back to the HTTP layer, which turns it into a `200` response.

Only steps 1 and 4's *edges* know about HTTP, storage, and notification technology. Step 3 - where the actual business meaning lives - is written in plain domain language and references none of them. That separation is the entire point of the hexagonal style: swap the web framework, swap the database, swap email for Slack, and the rules about what makes a valid decision never need to be touched, retested, or even re-read by whoever owns them.
