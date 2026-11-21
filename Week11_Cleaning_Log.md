# Week 11 cleaning log

Source file `Week11_Messy_Followup_Data.txt` contained comma-separated data. Its byte-for-byte working copy uses the CSV filename expected by the starter notebook. Both files had SHA-256 `081894eb00df916fd4f571a7f35eb69043df8c3fa445c8a1c5c7e7dc94207363`. All changes were made in notebook code on a separate DataFrame.

| Issue | Observed evidence | Decision | Rows affected | Verification |
|---|---|---|---:|---|
| Exact duplicates | Q005 and Q020 each appeared twice across all nine fields. Patient IDs also recur across different referrals. | Drop exact duplicate rows, retaining the first copy. Use record ID to verify the referral grain. | 2 removed | 42 raw rows, 40 cleaned rows, 40 unique record IDs, zero residual duplicates. |
| Clinic labels | NORTHSIDE CLINIC (2), MOUNTAIN VIEW CLINIC (1), RIVERBEND CLINIC (1), and River Bend Clinic (1) differed from approved labels. | Strip whitespace and map all observed variants to approved names in `clinic_clean`. | 5 relabeled | Raw-to-clean cross-tab and zero unmapped clinics. |
| Named service labels | medication review (2), social work (1), nutrition (1), and care coordination (1) appeared in lowercase. | Strip and case-fold for matching to the approved labels in `service_clean`. | 5 relabeled | Cross-tab and assertion that every nonblank normalized key maps. |
| Missing service | Q018 had a blank service. | Label it Unknown and retain the referral. | 1 relabeled | One Unknown in the cross-tab. The service summary reconciles to all 40 referrals. |
| Date parsing | ISO and U.S. month-first strings appeared in all three fields. Seven completion dates were blank. | Parse mixed formats with coercion and preserve missing completions. | 40 referral, 40 due, and 33 completion values parsed. Slash dates accounted for 7, 7, and 6 values respectively. | Missing counts stayed at 0, 0, and 7. All three fields have datetime dtypes. Export dates use YYYY-MM-DD. |
| Contact attempts | Q023 contained the text “two”. | Coerce to numeric and leave the unparseable value missing. | 1 newly missing | Missing count rose from zero to one. The raw-value check identified “two” and the result is numeric. |
| Outcome indicators | Seven completion dates were blank. Q040 had a recorded completion after the snapshot and before its due date. | Apply the assignment’s date-present and on-or-before-due rules. Check a strict October 31 completion cutoff separately. Both calculations use all 40 referrals. | 40 classified, 1 differs in the cutoff check | Cross-tab shows 20 on time, 13 completed late, and seven pending under the primary rule. The cutoff check has 19 on time and 32 completed. |

The export check rereads the cleaned CSV, confirms 40 rows and unique referral IDs, and checks the column order and date strings. The final cell rehashes the raw file and checks the raw DataFrame against a fresh read.

