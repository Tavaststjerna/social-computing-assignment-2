# MiniSocial - Coding Assignment 2: Moderation

Course project prepared for Eeli Tavaststjerna, Social Computing 2026-2027,
University of Oulu. AI assisted implementation; start with `ALOITA-TASTA.txt`. The `assignment2/` folder contains the
code walkthrough, report draft and silent video plan.

## Start on Windows

Open PowerShell in this directory:

```powershell
py -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe app.py
```

Open http://127.0.0.1:8080. The demo administrator is `admin` / `admin`.
Stop with Ctrl+C. A new server process may require a fresh login; an optional
`MINISOCIAL_SECRET_KEY` environment variable can supply a persistent secret.
This is a local course prototype, not a production deployment.

The included `database.sqlite` is the original course demo database, not the
student's private working database. Assignment 1 migrations happen automatically.
The tests never modify this database.

## Assignment 2 implementation

- `moderate_content(content)`: returns filtered text and content score, using
  the supplied censorship lists and the specification on `/rules`.
- `user_risk_analysis(user_id)`: applies the specified weighted content sum,
  account-age multiplier and maximum user risk of 5.0.
- `review_before_publish(...)`: Chapter 4, Design Claim 20. Risky post/comment
  submissions receive a private, editable review before publication. Members
  can recheck, cancel or publish the filtered version. Clean text has an
  optional **Check before posting** button.
- Admin risk ordering applies to the entire dataset before pagination.
- HTML autoescaping explicitly covers `.j2` templates. Review approvals are
  signed and tied to the text, author, target and anonymity choice.

Original content is retained for administrator review and risk scoring.
Public feed, post and profile pages display the moderated version.
Severe violations are publicly replaced by the required removal notice;
this does not delete the underlying database row.

The earlier Unanswered filter, anonymous discussions and weekly reply goal
are included. `recommend()` remains the original placeholder for Assignment 3;
it is deliberately not part of this submission.

## Run the tests

```powershell
.\.venv\Scripts\python.exe -m unittest discover -s tests -v
```

26 automated tests passed on Python 3.12 with Flask 3.1.3. These cover exact
scores and boundaries, public filtering, review/edit/approval flows, account
creation, authorization, HTML escaping, global risk sorting and Assignment 1
regressions. Each database test creates an isolated temporary database.
The student's Windows/Python 3.14 environment still needs a local smoke test.
A visual browser test was unavailable in the preparation environment.

## Specification choices and limits

- User-score formula is the explicit sum in Rule 2.2; it is not divided by 5.
- Only user risk is capped. Post/comment risk can exceed 5.
- Capitalization is measured in the original text before filtering.
- URLs with `http://`, `https://`, `ftp://` and `www.` are recognized.
  Bare domains and deliberately obfuscated links are not detected.
- Phrase matching accepts repeated whitespace and line breaks.
- This keyword-based prototype does not understand context, sarcasm or all
  attempts to evade moderation. Behavioral effectiveness has not been studied.
- Raw originals remain in the database; do not upload a private working
  database to a public repository. Use the untouched course demo instead.

## Sources and attribution

Original MiniSocial by Dr. Aku Visuri and Daniel Szabo:
https://github.com/Crowd-Computing-Oulu/mini_social_exercise

Assignment requirements: supplied `social computing.pdf`, pages 2 and 5.
Rules: bundled `templates/rules.html.j2`, rendered at `/rules`. The public
https://mini-social.szab.eu/rules endpoint returned HTTP 403 during verification,
so the current live version could not be compared with the bundled rules.

Chapter 4, Design Claim 20: reminders immediately before a potential rule
violation can reduce offenses. Verified against the authors' chapter draft,
PDF page 21 (draft pagination differs from the published book):
https://www.cs.cmu.edu/~kiesler/publications/2011/2011_regulating-behavior-online-communities.pdf

Original license retained in `LICENSE`.
