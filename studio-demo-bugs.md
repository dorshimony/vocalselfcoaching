# Studio Ledger — bug log

Bugs found in the studio management app (production and demo) and how each was fixed.
Severity: **Critical** = wrong money data · **High** = wrong lesson/student data · **Medium** = broken flow · **Low** = UI/UX.

| # | Date found | Severity | Area | Bug | Root cause | Fix | Verified by |
|---|---|---|---|---|---|---|---|
| 1 | 2026-11-03 | High | Calendar sync | A paid lesson moved in Google Calendar showed twice (old and new date) | Sync never removes a paid lesson that disappeared from the calendar | Paid/planned data moves to the student's next lesson, then the stale lesson is removed | Manual + data check |
| 2 | 2026-11-04 | Medium | Lesson actions | A prepaid lesson had no button to mark it held | Settle button was hidden for paid lessons | Added "✓ mark held" for prepaid lessons; auto-close prepaid lessons after they end | Manual |
| 3 | 2026-11-08 | Critical | Payments | "Pay at next lesson" showed only the old debt, not the lesson being paid for | Expected-payment rows included only deferred lessons | Include every lesson up to the payment date | Manual |
| 4 | 2026-11-08 | Critical | Payments | Recording a payment from the debts panel left lessons marked unpaid | Payment save did not link covered lessons | New payments mark covered lessons paid, oldest first | QA TC18 |
| 5 | 2026-11-09 | High | Notes | Notes typed in the institution tab were lost | Saving one field re-rendered the screen and wiped the field being typed | No re-render while typing + autosave 0.9 s after typing stops | Manual |
| 6 | 2026-11-09 | High | Calendar sync | To-do events ("edit video", "marketing") became fake students/lessons | To-do filter missed these verbs | Extended the to-do keyword filter | Data check |
| 7 | 2026-11-09 | High | Calendar sync | A lesson titled "X (via Y)" was assigned to the referrer Y | Student matching read the referrer's name | Ignore names after "via / referred by"; re-match existing lessons on sync | Manual |
| 8 | 2026-11-10 | Critical | Debts | A lesson covered by credit showed as "not paid" after it ended | Overdue check ignored a zero amount due | Lessons with nothing due are never overdue; auto-close them | Manual |
| 9 | 2026-11-12 | Medium | Lesson list | Marking a lesson held removed it from the list before payment could be recorded | Held lessons left "upcoming" immediately | Added explicit "held, not paid" flow; unpaid held lessons go to Debts | QA TC16–17 |
| 10 | 2026-11-12 | Critical | Debts | A student showed a false debt after a prepaid lesson | The same lesson existed twice and was charged twice | Duplicates (same calendar event) are charged once and cleaned up on load | QA TC20 |
| 11 | 2026-11-17 | Medium | Lesson actions | Package/credit lessons asked "held and paid" | Button logic only knew the explicit paid flag | Lessons fully covered by credit show only "mark held" | QA TC19 |
| 12 | 2026-11-18 | Critical | Income | Weekly expected income counted an institution day that did not happen | Pay counted for every scheduled day | Count only reported days; monthly check that the payment arrived by the 10th | Manual |
| 13 | 2026-11-18 | **Critical** | Lesson actions | "Mark held" / "held, not paid" created a duplicate lesson instead of updating it | Handler passed the lesson object's id, which the store does not include (undefined → new document) | Use the id from the button | QA TC16 (found by automated QA) |
| 14 | 2026-11-18 | Medium | Payments | Paying for a lesson marked "held, not paid" before it ended did not mark it paid | Covered-lessons search ignored held lessons that had not ended | Include held, unpaid lessons up to the payment date | QA TC18 |
| 15 | 2026-11-18 | Medium | Demo | Hidden hints showed in the lesson form (e.g. "this date is during a vacation") | Demo build dropped the base CSS reset for `[hidden]` | Restored the reset in the demo page | QA TC38 |
| 16 | 2026-11-18 | Low | Home | Lessons in the list were hard to tell apart | Thin separator line only | Each lesson is a separate card with spacing and an accent edge | QA TC39 |
| 17 | 2026-11-18 | Low | Home | Help button showed only "?"; the receipt link did not look like a button | Icon-only control; ghost link style | Labeled the help button; receipt link styled as a button | QA TC40 |
| 18 | 2026-11-18 | Low | UI (touch) | Tapping a lesson card left it tinted light blue | Hover styles stick on touch screens after a tap | Hover colours disabled on touch devices; no focus outline on tap | QA TC42 |
| 19 | 2026-11-18 | Medium | UX | Windows (student card, new lesson, etc.) had no clear way back | Only the backdrop or Esc closed a window | Sticky "✕ סגירה" button at the top of every window | QA TC41 |
| 20 | 2026-11-21 | Medium | Themes | Regular-student lessons were dark green with dark text (unreadable) in some themes | Hard-coded green colours, and the phone's dark-mode rule ignored the chosen theme | Regular-student colours follow the theme (one pair per theme) | QA TC47 |
| 21 | 2026-11-21 | Low | Calendar | Cancelled lessons of a student who left still showed in the week grid | Grid showed every cancelled lesson | Hide cancelled lessons of inactive students | Manual |
| 22 | 2026-11-22 | Medium | Payments | A payment could mark a future lesson paid while an earlier held lesson stayed owed | Covered lessons were chosen by date only | Lessons that already took place are covered first, then upcoming ones | QA TC18 |
| 23 | 2026-11-22 | Low | UI | While scrolling a window, its content showed through behind the close button | Close bar faded to transparent at the bottom and did not span the window width | Solid full-width close bar with a divider; content scrolls under it | QA TC55 |
| 24 | 2026-11-22 | Medium | Forms | Saving with a missing field did nothing, with no explanation | The "missing field" message appeared behind the open window | Message shown in the window's top bar (what is missing) and the field is marked red and focused | QA TC54 |
| 25 | 2026-11-23 | Low | Institution day | The day plan did not show when the break is | Break existed only as a gap between lesson times | A break row (time and length) appears between lessons when the gap is 10 minutes or more | QA TC56–57 |
| 26 | 2026-11-23 | Low | UI (touch) | A text box grew to an oversized, mostly empty box while typing on the phone | Boxes had a fixed size with a drag-to-resize corner that a finger catches while scrolling | Text boxes fit their text (up to half the screen, then scroll); no resize corner on touch screens | QA TC58 |

## Test suite
Automated end-to-end checks (Playwright, run on both the demo and the production build): page load, all tabs, debts and credit math, weekly income, week navigation, held/unpaid flow, payments, package lessons, duplicate protection, add/validate student, student card, background scroll lock, message template, WhatsApp link, plan box order, persistence, reset, layout at 360 px and 1280 px, dark mode, RTL, render time, techniques tab (levels, add, topic filter, edit, persist, delete), missing-field messages, close bar while scrolling.
