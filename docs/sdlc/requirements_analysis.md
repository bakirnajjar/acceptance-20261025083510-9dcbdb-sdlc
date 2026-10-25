# Requirements analysis

Contract: `docs/sdlc/requirements_analysis.json` (`schema_version: 1.0`, `stage: requirements_analysis`). The stable IDs and criteria below mirror that contract.

## Source evidence and limitations

| Key | Exact source reference | Scope |
| --- | --- | --- |
| S | `{"source_type":"user_story","source_id":"user_story","locator":"characters 0-16000"}` | Complete pinned story: small password-reset application using the supplied sign-in wireframe and curated recovery standard; synthetic accounts; in-memory notification sink; document production limitations; automated tests; no deployment or deployment workflow. The story has no end marker. |
| A | `{"source_type":"attachment","source_id":"f83756d6-e565-4ae3-87b4-c6079e860ed3","locator":"sign-in-wireframe.png"}` | Image was supplied, but the attachment reader returned no image content. Neither layout nor labels nor an acceptance marker can be verified. The only evidenced sign-in element is the matching-run standard's “Forgot password?” link, **not** an observed image detail. |
| K | `{"source_type":"knowledge","source_id":"cce5b6f4-4162-4c51-b630-53f545d0ba49","locator":"v1, chunk 0"}` | Matching-run account recovery standard, ACC-STD-01–03: “Forgot password?” entry; identical request responses whether an account exists; five requests per account per hour; single-use links expiring after 30 minutes; sign out every active session after reset; notify the owner after every successful reset. Identically worded results from other runs are not used. |

Every key in the provenance column means the **exact** reference in the table above is present on that requirement in the JSON. The T001 coverage map and assessment informed the candidate identifiers but are not substituted for product evidence.

## Atomic requirements and acceptance criteria

### REQ-001 — Reset entry point (S, A, K)

The sign-in page offers a 'Forgot password?' link that starts a password-reset request.

- Given the sign-in page, when a user activates the 'Forgot password?' link, then the password-reset request flow becomes available.

### REQ-002 — Neutral request response (S, K)

The user-visible response to a reset request is identical whether or not the account exists.

- Given an existing and a nonexistent account identifier under equivalent conditions, when a reset request is made for each, then the user-visible responses are identical.

### REQ-003 — Request limit (S, K)

Reset requests are limited to five per account per hour.

- Given five reset requests for the same account identifier within one hour, when a sixth request is made within that hour, then the sixth does not issue another reset link, while its user-visible response remains subject to REQ-002.
- Given an account identifier with fewer than five requests in the applicable hour, when an otherwise valid request is made, then that request is eligible for a reset link.

### REQ-004 — Link issuance (S, K)

An eligible reset request for an existing account issues a reset link for that account.

- Given an existing synthetic account with a request below the hourly limit, when a reset is requested for that account, then a reset link for that account is issued through the recovery flow.

### REQ-005 — Link expiry (S, K)

A reset link is unexpired before 30 minutes from issuance and expires at or after 30 minutes from issuance.

- Given an issued link that is otherwise valid, when a reset is attempted less than 30 minutes after that link was issued, then the link is valid with respect to expiry and expiry does not prevent the reset.
- Given an issued link, when a reset is attempted 30 minutes or more after that link was issued, then the link is expired and cannot complete the reset.

### REQ-006 — Single use (S, K)

A reset link can successfully complete a password reset only once.

- Given a link that successfully completed one reset, when that same link is used again, then the subsequent attempt cannot complete another reset.

### REQ-007 — Password change (S, K)

Completing a reset with a valid link changes the linked account's password to the submitted new password.

- Given an unused, unexpired reset link for an account and a new password, when the reset completes, then the linked account has the submitted new password.

### REQ-008 — Session sign-out (S, K)

Completing a password reset signs the account out of every active session.

- Given multiple active sessions for an account, when a password reset for that account succeeds, then none of those sessions remains active.

### REQ-009 — Owner notification (S, K)

The account owner is notified after every successful password reset.

- Given an account owner, when a password reset for that account succeeds, then a notification to that owner for that reset is recorded after completion in the in-memory sink.
- Given an unsuccessful reset attempt, when notifications are inspected, then it has not produced a successful-reset notification.

### REQ-010 — Synthetic accounts (S)

The application uses synthetic accounts instead of real users' accounts.

- Given the runnable application's provided account data, when its accounts are inspected, then the accounts are synthetic rather than real users' accounts.

### REQ-011 — In-memory sink (S)

The application sends recovery notifications to an in-memory notification sink.

- Given a recovery message generated by the application, when the message is dispatched, then it can be observed in an in-memory sink without a live delivery service.

### REQ-012 — Production limitations (S)

The application documentation identifies its production limitations.

- Given the application documentation, when it is reviewed, then it identifies synthetic accounts and in-memory notifications as production limitations and does not present the demo as production-ready.

### REQ-013 — Automated tests (S, K)

The repository provides automated tests for password-reset behavior.

- Given the repository's automated test command, when tests run, then automated checks cover the reset entry point, account-existence-neutral responses, hourly request limit, link issuance, 30-minute expiry, single use, password change, all-session sign-out, successful-reset notification, and in-memory sink.

### REQ-014 — No deployment (S)

No deployment is performed as part of this work.

- Given the completed work, when its recorded actions and artifacts are reviewed, then no deployment has been performed.

### REQ-015 — No deployment workflow (S)

No deployment workflow is added to the repository.

- Given the completed repository changes, when workflow files and the diff are reviewed, then no deployment workflow has been introduced.

## Assumptions

- `acceptance-20261025083510-9dcbdb-skill-sentinel The acceptance requirements skill produced this output.`
- `acceptance-20261025083510-9dcbdb-same-agent-revision The 30-minute expiry threshold is measured from link issuance; no clock implementation is prescribed.`
- The complete pinned story has no end marker. The attachment reader exposed no image contents or acceptance marker; none can be copied or inferred.
- Only the existence of a supplied sign-in wireframe is known from the image evidence. The matching-run recovery standard supplies the 'Forgot password?' link; no other visual specifics are assumed.
- The application supports demonstrable active sessions so that invalidation of every active session can be tested; no session storage or authentication technology is prescribed.
- Apply the five-per-account-per-hour limit to submitted account identifiers without revealing account existence; the standard does not specify fixed versus rolling hour windows.
- The in-memory sink makes issued recovery messages and successful-reset owner notifications observable for this demo; no live email or SMS service is assumed.
- Production-limitations documentation identifies at least synthetic accounts and in-memory notifications; any further limitations require implementation evidence.

## Open questions (all non-blocking; paired assumptions)

1. **What exact layout, labels, and acceptance marker, if any, are present in the sign-in wireframe?** Assumption: only its existence as a sign-in wireframe is known; the matching-run standard supports the 'Forgot password?' link, and no other visual specifics or marker are inferred.
2. **How will active sessions be represented in the small application?** Assumption: the application supports demonstrable active sessions to test every-session sign-out, without prescribing storage or authentication technology.
3. **How does the per-account hourly request limit apply to nonexistent identifiers, and are hour windows fixed or rolling?** Assumption: count submitted account identifiers without revealing account existence; do not prescribe fixed or rolling windows beyond the one-hour limit.
4. **Which delivery format or channel is intended for reset links and owner notifications?** Assumption: recovery messages and successful-reset notices are observable through the required in-memory sink; no live mail or SMS integration is required.
5. **Which production limitations beyond synthetic accounts and in-memory notifications should be documented?** Assumption: document those two evidenced demo limitations at minimum; include further limitations only when supported by the implementation.
