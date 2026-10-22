# Phase 1 — restore plan (PII-free)

Scope: sanitisation of the free-text columns `stage_title_ar` and `stage_subtitle_ar` in the
`application_events` table, plus access restriction of three table views, narrowing of three public
API responses, and two write-path fixes.

**This file contains no personal data.** The byte-exact restore point (which does contain applicant
names, wallet identifiers and one disbursement reference) lives in the git-ignored local folder:

```
.local/security/application_events_snapshot_2026-10-22.json
```

## 1. Affected records

10 of the 13 `application_events` rows carry personal data in their free text. The 3 remaining rows
(`SC-2026-6120` / `contract_docs`, `SC-2026-8492` / `disbursement_approval`,
`SC-2026-9104` / second `eligibility_wallet_check` row) are untouched — no names, no identifiers.

| # | Row id | Reference | Stage | Field | Template before | Template after |
|---|---|---|---|---|---|---|
| 1 | `01a1607f-0e49-763d-8f49-d2a39fb4164b` | SC-2026-8492 | committee_decision | `stage_title_ar` | `طلب تمويل #{ref} — {applicant_name}` | `طلب تمويل #{ref}` |
| 2 | `01a1607f-0e4b-786e-8265-ab27671a1aff` | SC-2026-7310 | disbursement_reconciliation | `stage_title_ar` | `صرف تمويل تضامني #{ref} — {applicant_name}` | `صرف تمويل تضامني #{ref}` |
| 3 | `01a1607f-0e4b-786e-8265-ab27671a1aff` | SC-2026-7310 | disbursement_reconciliation | `stage_subtitle_ar` | `مسار المؤسسات • تم التحقق من نتيجة الصرف عبر محفظة شام كاش ({disbursement_reference})` | `مسار المؤسسات • تم التحقق من نتيجة الصرف عبر محفظة شام كاش` |
| 4 | `01a1607f-0e4b-78e9-9097-ac9f298bd517` | SC-2026-9104 | eligibility_wallet_check | `stage_title_ar` | `تحقق أهلية ومحفظة #{ref} — {applicant_name}` | `تحقق أهلية ومحفظة #{ref}` |
| 5 | `01a160a8-dc3d-7517-936e-463c2b3b0b12` | SC-2026-1440 | submitted | `stage_title_ar` | `تقديم طلب تمويل #{ref} — {applicant_name}` | `تقديم طلب تمويل #{ref}` |
| 6 | `01a160a8-dc3d-7517-936e-463c2b3b0b12` | SC-2026-1440 | submitted | `stage_subtitle_ar` | `مسار المؤسسات • تم تسجيل معرّف المحفظة ({wallet_id}) للفحص` | `مسار المؤسسات • تم تسجيل معرّف المحفظة للفحص` |
| 7 | `01a160a9-1afd-75a0-8362-a4e56861b77d` | SC-2026-7762 | submitted | `stage_title_ar` | `تقديم طلب تمويل #{ref} — {applicant_name}` | `تقديم طلب تمويل #{ref}` |
| 8 | `01a160a9-1afd-75a0-8362-a4e56861b77d` | SC-2026-7762 | submitted | `stage_subtitle_ar` | `مسار الأفراد • تم تسجيل معرّف المحفظة ({wallet_id}) للفحص` | `مسار الأفراد • تم تسجيل معرّف المحفظة للفحص` |
| 9 | `01a160d1-b9b6-7500-a2ac-5099c8158fd0` | SC-2026-3025 | submitted | `stage_title_ar` | `تقديم طلب تمويل #{ref} — {applicant_name}` | `تقديم طلب تمويل #{ref}` |
| 10 | `01a160d1-b9b6-7500-a2ac-5099c8158fd0` | SC-2026-3025 | submitted | `stage_subtitle_ar` | `مسار المؤسسات • تم تسجيل معرّف المحفظة ({wallet_id}) للفحص` | `مسار المؤسسات • تم تسجيل معرّف المحفظة للفحص` |
| 11 | `01a1614b-3683-7c6c-bc94-1454f8d7e72a` | SC-2026-8492 | eligibility_wallet_check | `stage_subtitle_ar` | `تم تسجيل المعرّف ({wallet_id}) بحالة بانتظار التحقق الرسمي` | `تم تسجيل المعرّف بحالة بانتظار التحقق الرسمي` |
| 12 | `01a1614c-55e1-7f7e-a1e1-bef09909bcde` | SC-2026-8492 | eligibility_wallet_check | `stage_subtitle_ar` | `تم تسجيل المعرّف ({wallet_id}) بحالة بانتظار التحقق الرسمي` | `تم تسجيل المعرّف بحالة بانتظار التحقق الرسمي` |
| 13 | `01a161e3-a684-7b12-8143-28971fc16e3e` | SC-2026-9366 | submitted | `stage_title_ar` | `تقديم طلب تمويل #{ref} — {applicant_name}` | `تقديم طلب تمويل #{ref}` |
| 14 | `01a161e3-a684-7b12-8143-28971fc16e3e` | SC-2026-9366 | submitted | `stage_subtitle_ar` | `مسار الأفراد • تم تسجيل معرّف المحفظة ({wallet_id}) للفحص` | `مسار الأفراد • تم تسجيل معرّف المحفظة للفحص` |
| 15 | `01a161ea-028a-7b53-a431-da54d8a9f99e` | SC-2026-8492 | eligibility_wallet_check | `stage_subtitle_ar` | `تم تسجيل المعرّف ({wallet_id}) بحالة بانتظار التحقق الرسمي` | `تم تسجيل المعرّف بحالة بانتظار التحقق الرسمي` |

14 field changes across 10 rows.

Placeholders resolve from `loan_applications`:

- `{applicant_name}` → `loan_applications.applicant_name` for the reference code.
- `{wallet_id}` → `loan_applications.sham_cash_wallet_id` for the reference code.
- `{disbursement_reference}` → `loan_applications.disbursement_reference` for the reference code.

**Caveat:** row `01a1607f-0e4b-78e9-9097-ac9f298bd517` (SC-2026-9104) stores a shortened applicant
name in the event title that does not match the application row verbatim, so a re-derivation from the
table alone is not byte-exact for that row. Use the local snapshot file for the exact strings.

## 2. Restore procedure

1. Open the WeWeb editor, environment **editor**.
2. For each of the 10 affected rows, run `updateRowInWeWebTable`:

   ```
   tableName: application_events
   filters:   { "id": "<row id>" }
   data:      { "stage_title_ar": "<before value>", "stage_subtitle_ar": "<before value>" }
   ```

   Only send the columns listed in `fields_changed` of the local snapshot, so no other column is
   overwritten.
3. Verify by re-reading the table and comparing each row with the `before` values in the snapshot.

Restore is additive-only: no row was deleted, and no column was dropped, at any point in Phase 1.

## 3. Other Phase 1 changes (all reversible, no data loss)

| Change | Resource | Reverse action |
|---|---|---|
| New column `internal_note_ar` (text) | `loan_applications` | drop the column (or leave it empty) |
| New roles `admin`, `operator` | WeWeb Auth | delete the roles; users keep their other data |
| Role assignment on the owner account | `auth.users.roles` | `updateWeWebAuthUser` with `roles: []` |
| `accessRule` → `authenticated` + `accessRoles` | 3 table views | set `security: { accessRule: "public" }` |
| Whitelisted response columns | 3 backend workflows | restore the previous column list / return shape |
| Sanitised text templates | 2 backend workflows | restore the previous `stage_title_ar` / `stage_subtitle_ar` code |
| Timeline served by the PIN-verified endpoint | `/api/financing/track` + Track page | repoint the page repeat at `globalContext.tableViews['76335532']?.data` |

## 4. Explicitly out of scope in Phase 1

- No row deletions, no column drops, no data cleanup of the 9 QA applications or the QA event rows.
- No publishing, no production write, no new users, no SMS, no real financing application.
- No change to the visual structure of Home, Apply, Track or Hub pages.
- `tracking_pin_hash` still stores the plaintext 6-digit PIN (deferred to Phase 2/3 — it requires a
  hash + migration + a change in the `/track` comparison). It must never be selected in a table view.
