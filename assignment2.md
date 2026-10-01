## 1. Test Plan

### 1.1 Scope

#### In Scope
- Validation of transfer amounts from 100 to 500,000 KZT.
- Validation of whole-tenge amounts.
- Validation of the 1,000,000 KZT daily transfer limit.
- Validation of the SMS code requirement for transfers above 100,000 KZT.
- SMS code expiration after 120 seconds.
- Cancellation of the transfer after three incorrect SMS code attempts.
- Priority of the amount error when both the transfer amount and daily limit are violated.

#### Out of Scope
- Login and authentication functionality.
- SMS delivery infrastructure itself.
- Bank account creation and management.
- Transfers in currencies other than KZT.
- Performance and load testing.
- Security testing beyond the specified SMS-code behavior.

### 1.2 Test Approach

The money transfer feature will be tested using the following techniques:

- Equivalence partitioning and boundary value analysis for transfer amounts.
- Decision table testing for the interaction between transfer amount, daily limit, and SMS authorization.
- State transition testing for the SMS code flow.
- Positive and negative functional testing to verify valid and invalid transfer scenarios.

### 1.3 Entry Criteria

Testing can begin when:

- The money transfer feature is deployed to the test environment.
- The transfer requirements are available and agreed.
- Test accounts and required test data are available.
- The test environment is accessible and functioning.
- Basic smoke testing has passed.
- The SMS verification mechanism is available for testing.

### 1.4 Exit Criteria

Testing can be considered complete when:

- All planned test cases have been executed.
- All requirements in scope have at least one corresponding test case.
- No unresolved critical defects remain.
- High-severity defects and their risks have been reviewed and accepted or fixed.
- The SMS-code flow has been tested for successful, incorrect-code, and expiration scenarios.
- Remaining defects and residual risks are documented.

### 1.5 Top 3 Product Risks

1. **Incorrect transfer amount validation**
   
   The system may accept amounts below 100 KZT or above 500,000 KZT, potentially causing invalid transfers.

2. **Incorrect daily limit enforcement**
   
   The system may allow transfers that exceed the 1,000,000 KZT daily limit, potentially allowing customers to transfer more money than permitted.

3. **SMS authorization failure**
   
   The system may incorrectly handle SMS-code expiration or incorrect attempts, potentially allowing an unauthorized transfer or incorrectly cancelling a valid transfer.

## 2. Test Cases

The following test cases are based on the test design from Assignment 1. Each test case contains
specific preconditions, concrete test data, reproducible steps, and a checkable expected result.

### TC-001 — Transfer amount below minimum

**Requirement:** Transfer amount must be between 100 and 500,000 KZT.

**Technique:** Boundary Value Analysis

**Preconditions:**
- User is logged in.
- User has a balance of 500 KZT.
- Daily transfer total is 0 KZT.

**Steps:**
1. Open the money transfer screen.
2. Enter `99` KZT as the transfer amount.
3. Tap **Transfer**.

**Expected result:**
- The system displays an error indicating that the transfer amount must be at least 100 KZT.
- The transfer is not created.
- The account balance remains 500 KZT.
- The daily transfer total remains 0 KZT.

---

### TC-002 — Minimum valid transfer amount

**Requirement:** Transfer amount must be between 100 and 500,000 KZT.

**Technique:** Boundary Value Analysis

**Preconditions:**
- User is logged in.
- User has a balance of 500 KZT.
- Daily transfer total is 0 KZT.

**Steps:**
1. Open the money transfer screen.
2. Enter `100` KZT as the transfer amount.
3. Tap **Transfer**.

**Expected result:**
- The transfer is accepted.
- No SMS code is requested because the amount is not above 100,000 KZT.
- The transfer is completed.
- The account balance becomes 400 KZT.
- The daily transfer total becomes 100 KZT.

---

### TC-003 — Transfer amount at SMS threshold

**Requirement:** Transfers above 100,000 KZT require a 6-digit SMS code.

**Technique:** Boundary Value Analysis

**Preconditions:**
- User is logged in.
- User has a balance of 200,000 KZT.
- Daily transfer total is 25,000 KZT.

**Steps:**
1. Open the money transfer screen.
2. Enter `100,000` KZT as the transfer amount.
3. Tap **Transfer**.

**Expected result:**
- The transfer is accepted.
- An SMS code is not requested because the amount is not above 100,000 KZT.
- The transfer is completed.
- The account balance becomes 100,000 KZT.
- The daily transfer total becomes 125,000 KZT.

---

### TC-004 — Transfer above SMS threshold

**Requirement:** Transfers above 100,000 KZT require a 6-digit SMS code.

**Technique:** Boundary Value Analysis

**Preconditions:**
- User is logged in.
- User has a balance of 300,000 KZT.
- Daily transfer total is 50,000 KZT.

**Steps:**
1. Open the money transfer screen.
2. Enter `100,001` KZT as the transfer amount.
3. Tap **Transfer**.

**Expected result:**
- The system sends a 6-digit SMS code.
- The SMS confirmation screen is displayed.
- The transfer is not completed before successful SMS verification.
- The account balance remains 300,000 KZT until the transfer is confirmed.

---

### TC-005 — Maximum valid transfer amount

**Requirement:** A transfer can be between 100 and 500,000 KZT.

**Technique:** Boundary Value Analysis

**Preconditions:**
- User is logged in.
- User has a balance of 700,000 KZT.
- Daily transfer total is 100,000 KZT.
- A valid SMS code can be received.

**Steps:**
1. Open the money transfer screen.
2. Enter `500,000` KZT as the transfer amount.
3. Tap **Transfer**.
4. Enter the correct 6-digit SMS code.
5. Confirm the transfer.

**Expected result:**
- The transfer is accepted after successful SMS verification.
- The transfer is completed.
- The account balance becomes 200,000 KZT.
- The daily transfer total becomes 600,000 KZT.

---

### TC-006 — Transfer amount above maximum

**Requirement:** A transfer cannot exceed 500,000 KZT.

**Technique:** Boundary Value Analysis

**Preconditions:**
- User is logged in.
- User has a balance of 600,000 KZT.
- Daily transfer total is 50,000 KZT.

**Steps:**
1. Open the money transfer screen.
2. Enter `500,001` KZT as the transfer amount.
3. Tap **Transfer**.

**Expected result:**
- The system displays an error indicating that the maximum amount for one transfer is 500,000 KZT.
- The transfer is not created.
- The account balance remains 600,000 KZT.
- The daily transfer total remains 50,000 KZT.
- An SMS code is not requested.

---

### TC-007 — Invalid transfer amount takes priority over daily-limit violation

**Requirement:** If both the amount and daily limit are violated, the amount error is shown.

**Technique:** Decision Table Testing

**Preconditions:**
- User is logged in.
- User has a balance of 1,000,000 KZT.
- Daily transfer total is 900,000 KZT.

**Steps:**
1. Open the money transfer screen.
2. Enter `500,001` KZT as the transfer amount.
3. Tap **Transfer**.

**Expected result:**
- The system displays the amount validation error.
- The amount error is displayed instead of the daily-limit error.
- The transfer is not created.
- The account balance remains 1,000,000 KZT.
- The daily transfer total remains 900,000 KZT.

---

### TC-008 — Daily transfer limit exceeded

**Requirement:** The daily transfer limit is 1,000,000 KZT across all transfers.

**Technique:** Decision Table Testing

**Preconditions:**
- User is logged in.
- User has a balance of 1,500,000 KZT.
- Daily transfer total is 900,000 KZT.

**Steps:**
1. Open the money transfer screen.
2. Enter `250,000` KZT as the transfer amount.
3. Tap **Transfer**.

**Expected result:**
- The system displays a daily-limit error.
- The transfer is not created.
- The account balance remains 1,500,000 KZT.
- The daily transfer total remains 900,000 KZT.

---

### TC-009 — Valid transfer below SMS threshold

**Requirement:** Transfers at or below 100,000 KZT do not require SMS verification.

**Technique:** Decision Table Testing

**Preconditions:**
- User is logged in.
- User has a balance of 100,000 KZT.
- Daily transfer total is 10,000 KZT.

**Steps:**
1. Open the money transfer screen.
2. Enter `50,000` KZT as the transfer amount.
3. Tap **Transfer**.

**Expected result:**
- The transfer is approved immediately.
- No SMS code is requested.
- The account balance becomes 50,000 KZT.
- The daily transfer total becomes 60,000 KZT.

---

### TC-010 — Valid transfer above SMS threshold requires verification

**Requirement:** Transfers above 100,000 KZT require a 6-digit SMS code.

**Technique:** Decision Table Testing

**Preconditions:**
- User is logged in.
- User has a balance of 300,000 KZT.
- Daily transfer total is 50,000 KZT.
- SMS delivery is available.

**Steps:**
1. Open the money transfer screen.
2. Enter `150,000` KZT as the transfer amount.
3. Tap **Transfer**.
4. Observe the confirmation screen.

**Expected result:**
- The system sends a 6-digit SMS code.
- The SMS confirmation screen is displayed.
- The transfer is not completed before the correct code is entered.
- The account balance remains 300,000 KZT while the transfer is awaiting confirmation.

---

### TC-011 — Correct SMS code confirms transfer

**Requirement:** Transfers above 100,000 KZT require a 6-digit SMS code.

**Technique:** State Transition Testing

**Preconditions:**
- User is logged in.
- User has a balance of 300,000 KZT.
- Daily transfer total is 50,000 KZT.
- A transfer of 150,000 KZT has been initiated.
- A valid 6-digit SMS code has been received.
- The SMS code has not expired.

**Steps:**
1. Open the SMS confirmation screen.
2. Enter the correct 6-digit SMS code.
3. Tap **Confirm**.

**Expected result:**
- The SMS code is accepted.
- The transfer is confirmed.
- The account balance becomes 150,000 KZT.
- The daily transfer total becomes 200,000 KZT.
- The transfer enters the confirmed state.

---

### TC-012 — Three incorrect SMS codes cancel the transfer

**Requirement:** Three wrong SMS-code attempts cancel the transfer.

**Technique:** State Transition Testing

**Preconditions:**
- User is logged in.
- User has a balance of 300,000 KZT.
- Daily transfer total is 50,000 KZT.
- A transfer of 150,000 KZT has been initiated.
- The SMS confirmation screen is displayed.

**Steps:**
1. Enter an incorrect 6-digit SMS code.
2. Submit the code.
3. Enter another incorrect 6-digit SMS code.
4. Submit the code.
5. Enter a third incorrect 6-digit SMS code.
6. Submit the code.

**Expected result:**
- After the first incorrect code, the transfer remains awaiting verification.
- After the second incorrect code, the transfer remains awaiting verification.
- After the third incorrect code, the transfer is cancelled.
- The transfer is not completed.
- The account balance remains 300,000 KZT.
- The daily transfer total remains 50,000 KZT.

## 3. Traceability Matrix

The traceability matrix maps each requirement to the test cases that verify it.
It also identifies requirements that are not fully covered by the current test cases.

| Requirement | Test Cases | Technique | Status |
|---|---|---|---|
| REQ-1: Transfer amount must be between 100 and 500,000 KZT, inclusive. | TC-001, TC-002, TC-005, TC-006 | Boundary Value Analysis | Covered |
| REQ-2: Transfer amounts must use whole tenge only. | — | Equivalence Partitioning | NOT COVERED |
| REQ-3: Daily transfer limit is 1,000,000 KZT across all transfers. | TC-008, TC-009, TC-010 | Decision Table Testing | Covered |
| REQ-4: Transfers above 100,000 KZT require a 6-digit SMS code. | TC-003, TC-004, TC-009, TC-010, TC-011 | Boundary Value Analysis / Decision Table / State Transition | Covered |
| REQ-5: SMS code is valid for 120 seconds. | — | State Transition Testing | NOT COVERED |
| REQ-6: Three incorrect SMS-code attempts cancel the transfer. | TC-012 | State Transition Testing | Covered |
| REQ-7: If both the amount and daily limit are violated, the amount error is shown. | TC-007 | Decision Table Testing | Covered |

## 4. SMS Code Release Checklist

- [ ] A 6-digit SMS code is requested for transfers above 100,000 KZT.
- [ ] Transfers of exactly 100,000 KZT do not require an SMS code.
- [ ] The SMS code is accepted when the correct code is entered within 120 seconds.
- [ ] An SMS code is rejected after 120 seconds have elapsed.
- [ ] The first incorrect SMS code attempt does not cancel the transfer.
- [ ] The second incorrect SMS code attempt does not cancel the transfer.
- [ ] The third incorrect SMS code attempt cancels the transfer.
- [ ] A transfer is not completed when SMS verification fails or the code expires.

## 5. Defect Reports

The following defect reports are mock defects created from the specified requirements and test scenarios.
They illustrate how defects would be documented if the described behavior were observed during testing.

### DEF-001 — Transfer above daily limit is approved

**Title:** Transfers: transfer is approved when the daily limit is exceeded

**Environment:**
- Test environment
- Web application
- Browser: MISSING
- Application version: MISSING

**Preconditions:**
- Test user is logged in.
- Account balance is 1,500,000 KZT.
- Daily transfer total is 900,000 KZT.

**Steps to reproduce:**
1. Open the money transfer screen.
2. Enter `250,000` KZT as the transfer amount.
3. Tap **Transfer**.
4. Complete any required confirmation.

**Expected result:**
- The system displays a daily-limit error because the resulting daily total would be 1,150,000 KZT.
- The transfer is not completed.

**Actual result:**
- The transfer is approved even though the resulting daily total exceeds 1,000,000 KZT.

**Reproducibility:** 5 of 5 attempts

**Severity:** High

**Priority:** High

**Evidence:**
- Screenshot of the successful transfer: MISSING
- Request ID / logs: MISSING

**Traces to:** REQ-3 / TC-008


### DEF-002 — Expired SMS code confirms transfer

**Title:** SMS confirmation: expired code is accepted after 120 seconds

**Environment:**
- Test environment
- Web application
- Browser: MISSING
- Application version: MISSING

**Preconditions:**
- Test user is logged in.
- Account balance is 300,000 KZT.
- Daily transfer total is 50,000 KZT.
- A transfer of 150,000 KZT has been initiated.
- A valid 6-digit SMS code has been received.

**Steps to reproduce:**
1. Open the SMS confirmation screen.
2. Wait for more than 120 seconds.
3. Enter the previously received 6-digit SMS code.
4. Tap **Confirm**.

**Expected result:**
- The system rejects the SMS code because it has expired.
- The transfer is not confirmed.
- The account balance remains unchanged.

**Actual result:**
- The system accepts the expired SMS code.
- The transfer is confirmed.

**Reproducibility:** 5 of 5 attempts

**Severity:** High

**Priority:** High

**Evidence:**
- Screen recording showing the expiration period and successful confirmation: MISSING
- Request ID / logs: MISSING

**Traces to:** REQ-5 / TC-009


### DEF-003 — Fourth incorrect SMS attempt does not cancel transfer

**Title:** SMS confirmation: transfer remains active after three incorrect codes

**Environment:**
- Test environment
- Web application
- Browser: MISSING
- Application version: MISSING

**Preconditions:**
- Test user is logged in.
- Account balance is 300,000 KZT.
- Daily transfer total is 50,000 KZT.
- A transfer of 150,000 KZT has been initiated.
- The SMS confirmation screen is displayed.

**Steps to reproduce:**
1. Enter an incorrect 6-digit SMS code.
2. Submit the code.
3. Enter another incorrect 6-digit SMS code.
4. Submit the code.
5. Enter a third incorrect 6-digit SMS code.
6. Submit the code.
7. Observe the transfer state.

**Expected result:**
- The transfer is cancelled after the third incorrect SMS code.
- The transfer cannot be completed using the cancelled SMS verification flow.
- The account balance remains unchanged.

**Actual result:**
- The transfer remains active after the third incorrect SMS code.
- The system continues to allow SMS-code attempts.

**Reproducibility:** 5 of 5 attempts

**Severity:** High

**Priority:** High

**Evidence:**
- Screen recording of the three incorrect attempts: MISSING
- Request ID / logs: MISSING

**Traces to:** REQ-6 / TC-012

## 6. AI Appendix

AI was used as a supporting tool during the preparation of this documentation.
The final test cases, traceability matrix, checklist, and defect reports were reviewed
and edited by the student.

### 6.1 Prompts Used

#### Prompt 1 — Test Case Formatting

> Here are my test cases from Assignment 1. Convert them into full runnable test
> cases with the following fields: requirement, technique, preconditions, steps,
> and expected result. Use only the information provided and do not invent
> application behavior.

#### Prompt 2 — Traceability Matrix

> Based on these requirements and test cases, create a traceability matrix
> containing requirement, test cases, testing technique, and coverage status.
> Identify requirements that are not covered by the selected test cases.

#### Prompt 3 — SMS Release Checklist

> Create a release checklist for the SMS-code flow based only on the provided
> requirements. Keep it to no more than 12 items and write each item as something
> that can be checked during release testing.

#### Prompt 4 — Defect Reports

> Create three mock defect reports for the money transfer feature. Include title,
> environment, preconditions, steps to reproduce, expected result, actual result,
> reproducibility, severity, priority, evidence, and requirement/test traceability.
> Do not invent real environment information; mark unavailable information as
> MISSING.

### 6.2 Raw AI Output

The raw AI output was reviewed rather than copied directly into the final
documentation. The AI-generated material included proposed test-case structures,
traceability mappings, a release checklist, and mock defect reports.

### 6.3 Changes Made to AI Output

The following changes were made:

1. Test cases were checked against the original requirements and Assignment 1.
2. Test data and expected results were adjusted to match the specified transfer
   limits and SMS-code rules.
3. The test cases were rewritten to contain concrete preconditions and numbered
   reproducible steps.
4. The traceability matrix was checked to ensure that uncovered requirements were
   explicitly marked as NOT COVERED.
5. The SMS release checklist was limited to 12 items and written as verification
   points rather than detailed test procedures.
6. The defect reports were explicitly identified as mock defects.
7. Unknown environment details and evidence were marked as MISSING rather than
   invented.
8. Severity and priority were reviewed separately because they represent different
   decisions.

### 6.4 Why These Changes Were Made

The changes were made to ensure that the documentation reflects the actual
requirements and test design from Assignment 1 and does not contain unsupported
information.

In particular, missing information was not invented because defect reports are
evidence of observed or designed testing behavior. The final documentation was
reviewed by the student before submission.