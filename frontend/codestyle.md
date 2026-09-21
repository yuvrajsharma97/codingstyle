# CLAUDE.md

## Purpose

This file defines how the frontend codebase should be written.

The main goal is to produce code that is:

* easy to read
* easy to navigate
* easy to understand
* easy to modify
* difficult to accidentally break
* consistent across the application
* robust enough for a large application

The code should feel like it was written by a competent human developer who values simplicity and readability.

Do not write code that looks "AI-generated", overly abstract, unnecessarily clever, or architecturally impressive for its own sake.

When choosing between two technically valid implementations, prefer the implementation that looks and reads most like straightforward human-written code from the reference repository.

---

# 1. Match the Reference Coding Style

The reference project establishes the general coding style for this application.

Prefer:

* simple React components
* straightforward functions
* descriptive variable names
* direct JSX
* plain objects and arrays for data
* small event handlers
* explicit logic
* shallow abstractions
* readable control flow
* code that can be understood by reading from top to bottom

Avoid:

* excessive abstraction
* unnecessary design patterns
* complicated state machines
* deeply nested abstractions
* generic "frameworks" built inside the application
* excessive utility layers
* excessive configuration
* clever one-liners
* unnecessary functional programming
* abstractions created only to reduce a few repeated lines

The code should be **boring in a good way**.

A developer should be able to open a component and understand what it does without first understanding an internal architecture framework.

---

# 2. Simplicity Is the Default

Prefer the simplest implementation that correctly solves the problem.

For example, prefer:

```jsx
const handleSubmit = () => {
  if (!email) {
    setError("Email is required");
    return;
  }

  submitForm();
};
```

over creating multiple abstractions for simple validation.

Do not introduce an abstraction unless it provides a real benefit.

A good question before creating a helper, hook, wrapper, provider, utility, or abstraction is:

> "Will this make the code easier to understand and maintain?"

If the answer is no, do not create it.

---

# 3. Large Application Does Not Mean Every File Should Be Complex

The application may be very large.

That does **not** mean individual components should become sophisticated.

Large-scale organization belongs at the feature/module level.

Individual files should remain simple.

Prefer:

```text
features/
  projects/
    components/
    hooks/
    services/
    utils/
```

rather than creating a huge collection of global abstractions such as:

```text
core/
  architecture/
  abstractions/
  factories/
  managers/
  strategies/
  orchestrators/
```

unless there is a real need for them.

The goal is to organize complexity without putting unnecessary complexity inside individual files.

---

# 4. Project Organization

Use feature-oriented organization for large areas of the application.

A feature may contain:

```text
features/
  projects/
    components/
    hooks/
    services/
    utils/
    constants/
```

Shared application code can live in appropriate shared directories:

```text
components/
hooks/
services/
utils/
constants/
context/
```

Do not move everything into shared folders prematurely.

If code belongs clearly to one feature, keep it close to that feature.

---

# 5. Component Structure

Components should generally follow this order:

```jsx
import ...

const ComponentName = ({ ... }) => {
  const [state, setState] = useState(...);

  const value = ...;

  const handleSomething = () => {
    ...
  };

  return (
    ...
  );
};

export default ComponentName;
```

Keep the structure predictable.

Prefer reading a component from top to bottom:

1. imports
2. component declaration
3. state
4. derived values
5. hooks/context
6. handlers
7. JSX
8. export

Do not scatter related logic throughout the component.

---

# 6. Components Should Have One Clear Responsibility

A component should have a clear purpose.

Good:

```text
ProjectCard
ProjectList
ProjectFilters
ProjectModal
ProjectDetails
```

Avoid components that attempt to manage an entire feature.

For example, avoid a component that simultaneously:

* fetches multiple unrelated resources
* handles authentication
* manages several modals
* performs complex data transformations
* renders many unrelated UI sections
* contains unrelated business rules

However, do not split every small piece of JSX into its own component.

This is bad:

```text
Page
  Header
    HeaderTitle
      HeaderTitleText
  Content
    ContentWrapper
      ContentContainer
```

Only extract a component when it has a meaningful responsibility or is reused.

---

# 7. Prefer Explicit Code

If explicit code is easier to understand, use it.

Prefer:

```jsx
const handleDelete = async () => {
  try {
    await deleteProject(project.id);
    setProjects((currentProjects) =>
      currentProjects.filter((item) => item.id !== project.id)
    );
  } catch (error) {
    setError("Unable to delete project.");
  }
};
```

over creating several layers of abstraction just to hide a few lines.

Readable repetition is acceptable.

Do not abstract code simply because two pieces of code look similar.

---

# 8. Naming

Use descriptive names.

Prefer:

```text
currentQuestionIndex
selectedProject
isLoading
isModalOpen
handleSubmit
handleDelete
handleResponse
```

Avoid:

```text
x
obj
data2
temp
thing
stuff
```

Use `is`, `has`, `can`, or `should` for booleans.

Examples:

```jsx
isLoading
isOpen
hasPermission
canEdit
shouldShowModal
```

Event handlers should generally begin with `handle`.

Examples:

```jsx
handleSubmit
handleDelete
handleChange
handleClose
handleResponse
```

---

# 9. Keep JSX Readable

JSX should visually resemble the UI.

Prefer:

```jsx
return (
  <div className="project-card">
    <h2>{project.name}</h2>

    <p>{project.description}</p>

    <button onClick={handleDelete}>
      Delete
    </button>
  </div>
);
```

Avoid putting large amounts of business logic directly inside JSX.

Bad:

```jsx
{projects
  .filter(...)
  .map(...)
  .sort(...)
  .filter(...)
  .map(...)}
```

If the transformation becomes difficult to read, calculate it before the return.

---

# 10. Do Not Mutate React State

Never directly mutate state.

Bad:

```jsx
items.push(newItem);
setItems(items);
```

Bad:

```jsx
answers[0] = "NA";
setAnswers(answers);
```

Prefer:

```jsx
setItems((currentItems) => [...currentItems, newItem]);
```

For objects:

```jsx
setUser((currentUser) => ({
  ...currentUser,
  name: newName,
}));
```

The reference project may contain patterns that should not be copied when they are unsafe or incorrect.

Match its readability and simplicity, not its bugs.

---

# 11. State Management

Keep state as local as possible.

If only one component needs a value, keep it in that component.

Do not put everything into global state.

Use context/global state when multiple distant parts of the application genuinely need the same state.

Avoid duplicated sources of truth.

Bad:

```text
selectedProject
selectedProjectId
selectedProjectData
```

when they can all be derived from one source.

Prefer one authoritative state and derive the rest.

---

# 12. Derived Values

Do not store values in state if they can be calculated from existing state or props.

Avoid:

```jsx
const [items, setItems] = useState([]);
const [itemCount, setItemCount] = useState(0);
```

if `itemCount` is simply:

```jsx
const itemCount = items.length;
```

Keep derived data derived.

---

# 13. useEffect

Do not use `useEffect` for normal calculations.

Bad:

```jsx
useEffect(() => {
  setFullName(`${firstName} ${lastName}`);
}, [firstName, lastName]);
```

Prefer:

```jsx
const fullName = `${firstName} ${lastName}`;
```

Use `useEffect` primarily for side effects such as:

* API requests
* subscriptions
* browser APIs
* event listeners
* timers
* synchronization with external systems

Before adding an effect, ask:

> "Is this actually a side effect?"

If not, do not use `useEffect`.

---

# 14. API and Data Flow

Keep API calls out of presentation-heavy components when the logic becomes meaningful.

Prefer a flow like:

```text
Component
    ↓
Hook / Feature Logic
    ↓
Service
    ↓
Backend API
```

For example:

```text
ProjectList
    ↓
useProjects
    ↓
projectService
    ↓
Backend
```

The component should mainly be concerned with displaying the state and responding to user interaction.

Do not create unnecessary layers for a trivial API call, but do separate API logic when it becomes reusable or makes a component difficult to read.

---

# 15. Loading, Error, Empty, and Success States

Async UI should account for its important states.

Consider:

```text
Loading
Success
Empty
Error
```

Do not assume data always exists.

Example:

```jsx
if (isLoading) {
  return <Loading />;
}

if (error) {
  return <ErrorMessage message={error} />;
}

if (!projects.length) {
  return <EmptyState />;
}

return <ProjectList projects={projects} />;
```

Do not allow undefined API data to cause avoidable rendering crashes.

---

# 16. Error Handling

Errors should be handled deliberately.

Do not silently swallow errors.

Bad:

```jsx
try {
  await saveProject();
} catch (error) {}
```

Prefer:

```jsx
try {
  await saveProject();
} catch (error) {
  setError("Unable to save the project.");
}
```

User-facing errors should be understandable.

Do not expose raw backend errors unless they are intentionally safe and useful to users.

Technical details can be logged separately when appropriate.

---

# 17. Async Safety

Be careful with asynchronous operations.

Consider cases where:

* the component unmounts
* the user changes the selected item before a request finishes
* multiple requests are running at the same time
* an older request returns after a newer request

Do not allow stale responses to overwrite newer state.

For important async flows, use appropriate cancellation, request IDs, or other simple mechanisms.

Do not add complicated async abstractions unless the application actually needs them.

---

# 18. Forms

Forms should be predictable.

Handle:

* validation
* loading state
* submission errors
* disabled state when appropriate
* successful submission
* reset/close behavior where necessary

Avoid submitting multiple times accidentally.

Example:

```jsx
<button type="submit" disabled={isSubmitting}>
  {isSubmitting ? "Saving..." : "Save"}
</button>
```

Keep form logic readable rather than hiding it behind unnecessary abstractions.

---

# 19. Custom Hooks

Create a custom hook when it represents reusable behavior.

Good:

```text
useProjects
useAuth
useDebounce
useModal
```

Avoid hooks that exist only to move confusing code somewhere else.

Bad:

```text
useProjectPageEverything
useApplicationLogic
useCommonStuff
```

A hook should have a clear responsibility.

---

# 20. Utilities

Utilities should be small and predictable.

Good:

```text
formatDate()
formatCurrency()
truncateText()
validateEmail()
```

Avoid utility files containing unrelated functions simply because they are convenient places to put code.

If a utility is feature-specific, consider keeping it inside that feature.

---

# 21. Business Logic

Business rules should not be scattered throughout JSX.

If a rule becomes meaningful or complex, move it into a clearly named function, hook, or feature utility.

For example:

```jsx
const canEditProject = user.role === "admin" || project.ownerId === user.id;
```

is fine.

If the rule becomes substantially more complicated, extract it.

Do not create a massive domain architecture for simple frontend rules.

---

# 22. Abstraction Rules

Before introducing an abstraction, ask:

1. Is this code actually repeated?
2. Does the abstraction make the code easier to understand?
3. Will it likely be reused?
4. Does it reduce meaningful maintenance?
5. Would another developer understand it immediately?

If not, keep the code explicit.

Do not create:

* factories
* managers
* providers
* registries
* adapters
* strategy patterns
* generic wrappers

unless there is a real problem they solve.

---

# 23. Duplication

Do not blindly eliminate duplication.

Some repetition is better than a confusing abstraction.

Prefer:

```jsx
const handleProjectDelete = ...
```

and another small explicit handler when appropriate rather than immediately creating a generic:

```jsx
useEntityAction(...)
```

The goal is not "DRY at all costs."

The goal is maintainable code.

---

# 24. Performance

Do not optimize code before there is a reason.

Do not automatically add:

```jsx
useMemo
useCallback
memo
```

to every component.

Use optimization when:

* there is a demonstrated performance problem
* a calculation is genuinely expensive
* unnecessary renders are causing a real issue
* a stable reference is required for a specific reason

Readable code is the default.

---

# 25. Accessibility

UI should be usable with appropriate keyboard and assistive technology support.

Use semantic HTML where possible.

Prefer:

```jsx
<button>
```

over:

```jsx
<div onClick={...}>
```

Provide meaningful labels for form controls.

Do not remove focus indicators without replacing them appropriately.

Interactive elements should behave like interactive elements.

---

# 26. Defensive Rendering

Frontend code should expect imperfect data.

Handle cases such as:

```text
null
undefined
empty arrays
missing optional fields
slow responses
failed requests
unexpected API responses
```

Prefer safe rendering:

```jsx
{project?.name || "Untitled Project"}
```

when the field is genuinely optional.

Do not add defensive checks everywhere without reason.

The goal is sensible robustness, not paranoia.

---

# 27. Constants and Magic Values

Do not scatter important repeated values throughout the code.

Bad:

```jsx
if (status === 3) {
  ...
}
```

Prefer:

```jsx
const PROJECT_STATUS_COMPLETED = 3;
```

when the value has meaningful application-wide significance.

Do not create constants for trivial one-off values merely to avoid writing a literal.

---

# 28. Routing

Keep routing understandable.

Route-level components should primarily compose the relevant feature components.

Avoid putting an entire application's business logic inside route files.

A route should make it reasonably obvious which feature it belongs to.

---

# 29. Modals and Overlays

Modal state should be explicit.

Prefer:

```jsx
const [isDeleteModalOpen, setIsDeleteModalOpen] = useState(false);
```

over vague state such as:

```jsx
const [show, setShow] = useState(false);
```

When multiple modals exist, make their state distinguishable.

Do not create a giant global modal system unless the application genuinely requires one.

---

# 30. Authentication and Permissions

Do not assume authentication state is immediately available.

Account for:

```text
loading
authenticated
unauthenticated
```

Permission checks should happen consistently.

Do not rely only on hiding a button for security.

The backend remains responsible for actual authorization.

The frontend should still provide the correct user experience based on permissions.

---

# 31. Error Boundaries and Feature Isolation

Large features should not unnecessarily bring down unrelated parts of the application.

Where appropriate, use error boundaries around major areas of the application.

If one feature fails, consider whether the rest of the application can remain usable.

Do not add an error boundary to every tiny component.

---

# 32. Comments

Comments should explain **why**, not what the code obviously does.

Bad:

```jsx
// Set loading to true
setIsLoading(true);
```

Good:

```jsx
// Keep the previous results visible while the next page loads.
setIsLoadingMore(true);
```

Avoid excessive comments.

If code needs many comments to explain what it does, simplify the code first.

---

# 33. Dependencies

Do not add a dependency for a problem that can reasonably be solved with existing application code.

Before adding a package, consider:

* Is it genuinely needed?
* Does the application already have something similar?
* Will it add meaningful maintenance cost?
* Is the problem large enough to justify another dependency?

Avoid dependency bloat.

---

# 34. Do Not Rewrite Working Code Without a Reason

When modifying an existing feature:

* understand the existing code first
* make the smallest reasonable change
* preserve working behavior
* avoid unrelated refactoring
* do not rename everything unnecessarily
* do not introduce a new pattern just because it is theoretically cleaner

Consistency with the existing codebase is usually more valuable than theoretical perfection.

---

# 35. New Feature Process

When implementing a new feature:

### Step 1 — Understand the feature

Identify:

* what the user needs to do
* what data is involved
* what screens/components are required
* what existing components can be reused

### Step 2 — Find the correct feature location

Keep feature-specific code close together.

### Step 3 — Build the simplest useful structure

Start with:

```text
Page
  ↓
Feature Components
  ↓
Hooks / Logic
  ↓
Services
```

Only add additional layers when needed.

### Step 4 — Handle important states

Consider:

```text
Loading
Success
Empty
Error
```

### Step 5 — Test the important paths

Verify:

* normal usage
* invalid input
* failed requests
* empty data
* repeated actions
* navigation
* refresh/reload
* permission restrictions where relevant

---

# 36. Before Creating a New Component

Ask:

> "Does this actually represent a meaningful UI responsibility?"

If yes, create it.

If it is only five lines of JSX used once and has no meaningful responsibility, keeping it inside the parent may be clearer.

Do not optimize file count.

Optimize understandability.

---

# 37. Before Creating a New Hook

Ask:

> "Is this behavior reusable or complex enough that separating it improves readability?"

If not, keep the logic in the component.

---

# 38. Before Creating a New Utility

Ask:

> "Is this logic generic enough to be useful outside this component or feature?"

If not, keep it close to where it is used.

---

# 39. Before Creating Global State

Ask:

> "Do multiple independent parts of the application genuinely need this state?"

If not, keep it local.

---

# 40. Code Review Standard

When reviewing code, prioritize:

1. Correctness
2. Readability
3. Maintainability
4. Robustness
5. Consistency
6. Performance
7. Abstraction

Do not prioritize architectural sophistication over readability.

A simpler correct solution is usually better than a sophisticated solution that is harder to understand.

---

# 41. Avoid AI-Looking Code

Do not write code simply because it looks "professional" or "enterprise-grade."

Avoid patterns such as:

```text
AbstractBaseComponent
GenericEntityManager
UniversalDataProvider
ConfigurableStrategyFactory
GenericCRUDHook
```

when a straightforward implementation would work.

Avoid excessive comments explaining obvious code.

Avoid excessive TypeScript-style type ceremony if the project does not require it.

Avoid creating interfaces, wrappers, factories, and abstractions merely to demonstrate architectural thinking.

The code should look like a developer actually built the feature.

---

# 42. Reference Style vs. Engineering Improvements

The reference repository is the style reference, not an instruction to reproduce every implementation detail.

Copy the **style**, not the mistakes.

Good things to imitate:

* straightforward React
* simple component structure
* direct JSX
* descriptive naming
* practical handlers
* plain data structures
* limited abstraction
* readable top-to-bottom code
* pragmatic organization

Do not imitate:

* direct state mutation
* unsafe React keys
* vague variable names
* inconsistent naming
* unnecessary technical debt
* patterns that are clearly fragile

The target is:

> **Reference-project readability + production-level frontend safety.**

---

# 43. Preferred Mental Model

Think about frontend behavior as:

```text
User Action
    ↓
Component
    ↓
Handler
    ↓
Hook / Feature Logic
    ↓
Service
    ↓
API
    ↓
Response
    ↓
State Update
    ↓
UI
```

Do not introduce additional layers unless they solve an actual problem.

---

# 44. Final Rules

Always prefer:

```text
Simple > Clever
Explicit > Abstract
Readable > Compact
Predictable > Magical
Local state > Global state
Small components > Giant components
Meaningful reuse > Forced reuse
Real optimization > Premature optimization
Feature organization > Global architectural complexity
```

Most importantly:

> Write code that another developer can understand quickly.

> Write code that you can return to six months later without having to reverse-engineer it.

> Use architecture to organize complexity. Use simple code to implement behavior.

> Do not make code more abstract, generic, or architecturally sophisticated merely because this is a large application.

> When two solutions are equally valid, choose the one that looks and reads most like straightforward human-written code from the reference repository.

The final codebase should be:

**Boring to read, difficult to accidentally break, easy to navigate, and easy to change.**
