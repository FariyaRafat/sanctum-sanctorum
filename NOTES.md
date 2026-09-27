# Sanctum Sanctorum — Submission Notes

## Live Application

- **Live URL:** https://sanctum-sanctorum-9l9h.onrender.com
- **API Documentation:** https://sanctum-sanctorum-9l9h.onrender.com/docs
- **GitHub Repository:** https://github.com/FariyaRafat/sanctum-sanctorum

The deployed application and Swagger documentation were verified in a fresh browser.

## What I Completed

Implemented the required bookstore and lending-library functionality within the existing project structure:

- Book creation validation, including ISBN-13 checksum validation and duplicate ISBN handling.
- Book retrieval, partial updates, search, filtering, sorting, and pagination.
- Member validation, email uniqueness, member retrieval, tier-based access rules, and member statistics.
- Order validation, member/book checks, restricted-book access, pricing, membership and bulk discounts, stock reservation, payment, cancellation, and stock restoration.
- Loan persistence, borrowing rules, loan limits, restricted-book access, overdue detection, returns, stock restoration, and late-fee calculation.
- Member loan listing and top-books reporting.
- Frontend support for catalogue operations, book editing, ordering, borrowing, member views, loan management, and reports, with the Loans Return action remaining unresolved.
- Kept the provided test suite unchanged.

## Testing

The complete local automated test suite passes:

- **202 tests passed**
- **2 warnings**

The provided tests were not modified and were used as the acceptance criteria.

## Architecture and Design Decisions

I kept the existing FastAPI structure and implemented the functionality within its established layers rather than introducing a new framework or additional dependencies.

- **Routers** handle HTTP endpoints and request/response concerns.
- **Services** contain the main business rules for books, members, orders, loans, and reports.
- **SQLAlchemy models** represent the database entities and relationships.
- **Pydantic schemas** handle request validation and response serialization.
- Money values are represented as integer cents to avoid floating-point pricing issues.
- Time-dependent operations use the application's `get_now` dependency so they remain testable.
- The frontend remains a lightweight vanilla JavaScript application without an additional build step.

For deployment, I used **Render** because it provides a straightforward way to run the existing FastAPI application without requiring a separate frontend build or platform-specific API adapter.

The deployed application retains the project's existing SQLite setup. This keeps the implementation aligned with the starter project and preserves the local test setup. For a production system requiring durable shared storage, I would move the deployed database to a managed PostgreSQL service and configure the database connection explicitly for the deployment environment.

## What Remains

The backend loan-return workflow is implemented and covered by the automated tests. The remaining issue is limited to the **frontend Return action in the Loans section**.

I traced the initial cause to the click-delegation switch in `frontend/app.js`: the button renders with `data-action="loan-return"`, but there was no matching case for that action, so the click did nothing. I added the missing case, but the button still did not trigger the expected network request, indicating that another part of the frontend event flow is involved.

I did not have enough time before the deadline to isolate the remaining cause without making broader changes.

With more time, I would inspect the browser console for click-time errors, verify that the rendered button has the expected `data-id`, and step through `returnLoan()` with breakpoints to identify where the call chain stops.

## Specification Notes

I did not find the overall specification unclear, but two implementation details required an explicit implementation decision:

1. **Restricted-book state**

   The specification does not explicitly state whether a book's `restricted` flag should be snapshotted when an order or loan is created or checked using its current value.

   I used the book's **current `restricted` value** when validating new orders and loans. This is consistent with the specification's explicit price snapshot behavior and with the absence of a restriction-snapshot field in the data model. Therefore, a change to a book's restriction status affects subsequent orders or loans rather than retroactively changing an existing order.

2. **Overdue-loan borrowing rule**

   The specification states that a member with an overdue loan cannot borrow another book, but does not explicitly say whether the rule applies only to the same book or to the member's loans generally.

   I interpreted this as a **member-wide rule**: an overdue loan for any book prevents the member from borrowing another book. This interpretation is also consistent with the provided tests, which use an overdue loan on one book and a borrowing attempt for a different book.

## Git History

The original starter commit was preserved as the first commit.

Implementation work was developed through incremental commits for logical changes rather than being squashed into a single final commit. This keeps the development history readable and makes the progression of the implementation traceable.

## AI Usage

I used **ChatGPT to a limited extent** for clarifying a few requirements and for selected debugging and review questions.

I reviewed the suggestions against the existing code and assignment requirements before using them. I did not use AI as a substitute for testing or understanding the submitted implementation.

During frontend debugging, I did not adopt a broader suggested approach when it would have required unnecessary changes to the existing structure. I kept the final changes focused on the relevant parts of the application.