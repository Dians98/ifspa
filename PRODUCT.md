# Product

<!-- impeccable:product-schema 1 -->

## Platform

web

## Users

- **Primary: the secretariat / cashier** (`user` role). At a desktop PC at the counter, often with students or parents waiting, they register students, collect tuition payments (cash or Mobile Money), print receipts and look up a student's balance. They are moving from an Excel workbook that has become unmanageable.
- **Administrator** (`admin` role): the same application plus settings (programs, school years, fee schedules, user accounts), cancelling payments or batch admissions. There is no audit log (removed at the client's request). Daily use by other profiles (management, accounting) is not confirmed.
- **Public visitors** to the landing page: anyone looking the institute up (prospective students, parents, partners, authorities).

## Product Purpose

Replace the Excel tracking of tuition fees (*écolage*) at IFSPA with a web application that follows each student from enrollment to exit (graduation, dropout, expulsion):
- a stable student ID;
- month-by-month fee tracking across every year of study;
- year-to-year promotion in batches;
- printed receipts and fee statements.

Success means: at the counter, the secretariat answers "what does this student owe?" and records a payment with its receipt in a few seconds, without errors, and management can see at any time what has been collected and what is outstanding.

The public page is an **institutional showcase**: it establishes that the institute is serious and officially accredited. Student recruitment is secondary. It also carries the staff "Se connecter" button.

## Positioning

A tool built for one specific institute and its real rules, not a generic school ERP:
- ariary with no decimals;
- enrollment fee plus 10 monthly installments from October to July;
- cash and Mobile Money (MVola, Orange Money, Airtel Money);
- IF/SF student IDs that never change;
- receipts verifiable by QR code;
- promotion from one year to the next with repeaters handled.

## Operating Context

- **Place and equipment**: a desktop PC at the institute counter in Toamasina, Madagascar, with a **small thermal receipt printer (80 mm roll)** for payment receipts, and an ordinary printer for A4 fee statements.
- **Network**: the Internet connection is sometimes slow or unstable. Pages must stay light, and the interface must say clearly when an action has failed.
- **Recurring moments**:
  - collecting a payment while the parent waits;
  - printing the receipt;
  - chasing unpaid fees;
  - the batch year-to-year promotion at the start of the school year;
  - adjustments by the admin.
- **Documents produced**: payment receipts (numbered `R-YYYY-NNNNN`, with the amount in words and a QR code) and fee statements for parents.

## Capabilities and Constraints

- **Programs**: `IF` Infirmier(e)s and `SF` Sage-femmes. The program length can be configured (3 years by default).
- **Student ID**: `{PROGRAM}-{ENTRY_YEAR}-{NNNN}`, for example `IF-2026-0001`. It never changes.
- **Fees**: an annual enrollment fee plus monthly installments. Everyone in the same program, year and level pays the same rate, with no discounts. Payments are applied to the oldest unpaid installment first. There is **no concept of "arrears"** carried over from one year to the next (removed at the client's request): each school year's unpaid items stay on that year.
- **Roles**: `user` can neither delete nor cancel anything. Payments are never deleted; they are cancelled with a stated reason.
- **Batch admissions** go through a verification step before they are applied. An admin can undo them.
- **Dashboard**: figures, tables and lists only, with **no charts** (a user decision).
- **Language**: the interface is entirely in **French**.
- **Hosting**: shared cPanel hosting (Node.js App + PostgreSQL + SSH). No Docker in production.
- **Undecided**: the exact receipt printer; contact details, address, tax IDs (NIF/STAT) and photos for the landing page and the PDF headers.

## Brand Commitments

- **Name**: Institut de Formation Supérieur des Paramédicaux Atsinanana (**IFSPA**), Toamasina.
- **Official logo**: in `LOGO ET ARRËTEE IFSPA.pdf`. It shows the building, the map of Madagascar, a palm tree, a heart and the caduceus inside a sky-blue oval.
- **Accreditations**, to be quoted as they are:
  - decree no. 04460/2011-MESupRes (authorization to open);
  - decree no. 15482/2011-MESupRES (accreditation of the training, "Science de la Santé" field);
  - decree no. 23915/2011 CNEAT (administrative equivalence in the civil service);
  - renewal certificate no. 056/2024/MESupRes/SG/DGES/SG of 3 April 2024.
- **Standing visual preference (chosen 2026-09-30 in the landing direction round)**: the **category standard** for school websites, done with full care. That means a clear hero with a real photo, a readable grid and conventions embraced, with no quirky concept. The references were left to Claude as a quality bar, not as models to copy: the websites of the French-speaking health schools *La Source* (Lausanne), *HEdS Genève* and the *Faculté des sciences infirmières de l'Université de Montréal*.
- **Photos**: the real photos of the institute are not available yet; they will go in `data/photos/`. In the meantime the prototype uses Pexels photos, each labelled "Photo d'illustration", kept in `public/images/illustration/`. They must be replaced before going live. The hero photo shows sashes bearing another school's name.
- **Color constraint stated by the user**: start from the pink and blue associated with the paramedical field, but in toned-down variants combined with white or another color, never raw pink and blue. The final choice will be made on a demo page.

## Evidence on Hand

- Logo and accreditation decrees: `LOGO ET ARRËTEE IFSPA.pdf`.
- **Not available, must not be invented**: photos of the institute, testimonials, figures (enrollment, success rate, alumni), job-placement claims, address, phone and email. Mark them as placeholders until they are provided.

## Product Principles

1. **The counter first**: every flow is designed for a cashier with someone waiting in front of them. Fast search, few clicks, obvious confirmation.
2. **Nothing is lost, everything is traceable**: no destructive deletion; cancellations are dated, attributed to their author and given a reason; sensitive actions are logged.
3. **Check before committing**: grouped or irreversible operations (admissions, cancellations) show a summary and warnings before they run.
4. **Official accuracy**: amounts, student IDs, receipts and decrees must be exact and presentable to a parent or an authority.
5. **Sober and reliable on a modest connection**: clarity takes priority over effects.
