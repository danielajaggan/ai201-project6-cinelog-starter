# PR Response Doc — CineLog Watchlist Feature

## AI Usage
<!-- Fill in at the end — how you used AI tools during this project -->

## Comment 1 — Rename
**What I did:** Rename save_to_watchlist()to add_to_watchlist in services/watchlist_services.pyand updated all call sites
**How I verified:**ran the project wide search for save_to_watchlist across all .py files and comfirmed zero matches remained before committing.

## Comment 2 — Deduplication
**What I did:** Added an AlreadyOnWatchListError exception class and a deduplication check in add_to_watchlist() followint the same pattern as add_to_collection in collection_service.py. before creatin a new entry, the function now queries for an existing WatchListEntry with the same user_id and film_id. If one exists, it raises AlreadyOnWatchlistError instead of inserting a duplicate.
**How I verified:** by running the test with pytest to comfirm exsiting tests still pass.

## Comment 3 — Missing test
**What I did:** created tests/test_watchlist.py with two tests following the same fixtures and assertion structure as tests/test_collection.py. the first test (test_add_to_watchlist_nonexistent_fild_raises) mirrors test_add_to_collection_nonexistent_film_raises.  It passes a fake uuif and asserts filmnotfounderror is raised.the second tests mirrors test_add_to_collection_duplicate_raises and it adds the same film twoce and asserts  AlreadyOnWatchlistError is raised, then confirms only one entru exists in the database
**How I verified:** pytest tests/test_watchlist.py -v, both tests passed. Then ran the full suite pytest tests/ -v, all 6 tests passed with no regressions.

## Comment 4 — Default visibility
**My position:** I chose public=True as the default for new watchlist entries
**Reasoning:** Cinelog is designed as a social film tracking network and is meant to not be private. Its  core value proposition is discovering what friends want to watch and only works if watchlists are visable by default. setting public=False as the default would mean the social features are effectively opt in and most users neber change defaults.A platform buit aroubd discovery needs itscontent to be discoverable from the start.
**Tradeoff acknowledged:** The strongest counterargument is that comparable platforms like letterboxd default new lists to pruvate, letting users opt into sharing rather that opt out of it. A user who doesnt read settings carefullt could have their watchlists publicly visible before the intend to share is. This is a real privacy risk,especially for new users still exploring the platform. If CineLog's user research showed that users were surprised to find their lists public, I would reverse this default. The decision should be revisited once real user behavior data is available.

## Comment 5 — Sort order
**My position:**I agree with switching to date-added descending as the default sort order for watchlists.
**Reasoning:** The reviewer's point is well-taken — a watchlist represents films a user intends to watch, and recently added films are more likely to reflect current intent than films added months ago. Alphabetical order made sense to me initially as a browsing aid (easy to scan for a specific title), but it doesn't reflect how users actually interact with a watchlist in practice. A user who just added three films after a trailer session wants to see those films at the top, not buried somewhere in the middle of an alphabetical list.

**Engagement with reviewer's point:** The reviewer noted that most users want to see what they added recently — this matches how comparable features work on Letterboxd and Trakt, where recency is the default sort. Alphabetical is a useful secondary sort (useful for searching), but it shouldn't be the default. I'll update `get_watchlist()` to use `order_by(WatchlistEntry.date_added.desc())` to match the pattern already established in `get_collection()`.

## Comment 6 — Rebase
**What conflicted:**
**How I resolved it:**
**How I verified no conflict remains:**

## PR Description
<!-- Written at the end — feature overview, design decisions, manual testing steps -->