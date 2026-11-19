Project Brief: 1980–1989 New Wave, Electronic Dance & Alternative Chart Dataset

Objective

Build a verifiable dataset of individual songs that charted between January 1, 1980 and December 31, 1989, inclusive, across relevant US and UK music charts.

The genres of interest are:

* New Wave
* Electronic Dance
* Alternative

The goal is to identify every qualifying song, not merely popular or representative songs.

Do not create a playlist yet. Build and validate the underlying dataset first. A separate step will convert the final unique-song catalog into an Apple Music-compatible playlist.

⸻

1. Fundamental requirements

1. Collect actual historical chart records from documented sources.
2. Capture individual weekly chart appearances wherever the source provides them.
3. Preserve chart position, date, chart name, artist, and song title.
4. Deduplicate songs across charts while retaining all their chart appearances.
5. Apply genre classifications separately from chart membership.
6. Record source provenance for every record.
7. Never invent, estimate, or manually fabricate missing chart entries.
8. Clearly identify gaps in source coverage.

The dataset must distinguish between:

* Chart appearances: A song appearing on a particular chart in a particular week.
* Unique songs: A normalized song identity, potentially appearing on multiple charts.
* Genre classifications: Whether a song qualifies as New Wave, Electronic Dance, Alternative, or more than one.

A song appearing 20 weeks on a chart is one unique song, not 20 songs.

⸻

2. Chart sources to investigate

Prioritize primary or well-documented historical chart data.

United States

Chart	Coverage / purpose
Billboard Hot 100	Mainstream chart appearances
Billboard Dance/Disco Club Play	Dance-oriented club chart
Billboard 12-inch Singles Sales	Dance-oriented sales chart
Billboard Modern Rock Tracks	Alternative chart; began in 1988
Billboard Mainstream Rock	Supplemental alternative/rock coverage
Cash Box Top 100	Independent mainstream chart source

United Kingdom

Chart	Coverage / purpose
Official UK Singles Chart	Mainstream chart appearances
UK Indie Singles Chart	Independent releases and alternative music

Investigate other credible historical charts if they materially improve coverage. Document why each additional chart is included.

Do not assume that every chart existed throughout the entire decade.

Important distinction

There was no dedicated Billboard New Wave chart covering the decade. New Wave must therefore be treated as a genre classification, not as a chart name.

Do not confuse Billboard’s various dance chart formats or assume they are interchangeable.

⸻

3. Candidate data sources

Investigate these sources first. Verify availability, licensing, completeness, and data structure before relying on them.

ChartVault API

* Documentation: https://www.chartvault.fm/api
* API base: https://www.chartvault.fm/api/v1
* Supports historical chart issues, song histories, and chart records.
* Requires an API key. Investigate obtaining its free noncommercial key.
* Documented chart codes include Billboard Hot 100 and Modern Rock, but the documented list does not appear to include historical Billboard dance charts.

Cash Box Archives

* https://www.cashboxarchives.com/archives.htm
* Historical weekly Cash Box charts.
* Investigate how to systematically extract the 1980–1989 Pop Singles records.

UK chart data

* StatJam dataset: https://statjam.org/data/array-ukcharthits.min.json
* Source information: https://statjam.org/ataglance/culture-and-entertainment/list-music-ukcharthits.html
* Note: This dataset appears to contain chart-entry records rather than a complete weekly history.

UK weekly chart dataset

* https://github.com/JackDanHollister/UkTop100Scrape
* Investigate the available Excel datasets for weekly chart positions.

Billboard Hot 100 dataset

* https://github.com/mhollingshead/billboard-hot-100
* Investigate the historical weekly JSON data.
* Cross-check against another source where possible.

These are starting points, not an instruction to accept their contents uncritically.

⸻

4. Data acquisition strategy

For each source:

1. Determine whether the data covers all of 1980–1989.
2. Determine whether it provides weekly chart positions or only chart-entry events.
3. Download or retrieve the original data.
4. Preserve the original source files unchanged.
5. Extract records for the target decade.
6. Normalize them into the common schema below.
7. Record missing years, weeks, chart issues, or other coverage gaps.
8. Validate record counts and spot-check against original chart publications.

If a source requires an API key, registration, payment, or manual access, document that requirement. Do not pretend to have accessed data that was unavailable.

If historical Billboard dance chart data cannot be acquired, explicitly report that limitation rather than substituting a different chart.

⸻

5. Required data model

Use separate tables or CSV files for chart appearances, unique songs, genre classifications, and sources.

A. chart_appearances.csv

One record per song per chart issue.

Required fields:

Field	Description
appearance_id	Unique record identifier
chart_name	Original chart name
chart_organization	Billboard, Cash Box, Official Charts, etc.
country	US or UK
chart_date	Date of the chart issue
position	Position on that issue
song_id	Link to normalized song record
original_title	Title as published by the source
original_artist	Artist as published by the source
source_id	Reference to source register
source_record_id	Original record identifier, if available

Include peak position and weeks on chart if available, but do not substitute summary statistics for individual weekly appearances.

B. songs.csv

One record per unique song.

Field	Description
song_id	Stable unique identifier
title	Normalized song title
artist	Normalized primary artist
original_title_variants	Alternate title spellings
original_artist_variants	Alternate artist spellings
release_year	If verified
is_cover_or_version	If applicable
notes	Relevant identity or matching notes

Do not merge different recordings simply because they share a title.

Different versions, remixes, live recordings, and re-recordings should remain distinguishable when chart records identify them as separate releases.

C. genre_classifications.csv

One record per song per genre classification.

Field	Description
song_id	Song identifier
genre	New Wave, Electronic Dance, or Alternative
classification	Confirmed, probable, or disputed
evidence	Brief explanation
evidence_source	Reference to supporting source
confidence	High, medium, or low

Genre classification must not be inferred solely from chart appearance.

A song may qualify for multiple genres. Do not force every song into exactly one category.

D. sources.csv

One record per data source.

Include:

* Source ID
* Publisher / organization
* Chart name
* URL
* Years covered
* Data format
* Retrieval date
* Access method
* Known limitations
* Licensing / usage restrictions
* Reliability notes

⸻

6. Genre classification methodology

This is a critical part of the project.

New Wave

Identify songs commonly classified as New Wave by credible music references, contemporary publications, or documented genre histories.

Do not classify every synth-pop, post-punk, or electronic song as New Wave automatically.

Electronic Dance

Include songs with a credible connection to electronic dance music, dance-pop, club music, Hi-NRG, house, electro, or related dance genres.

Distinguish dance-oriented recordings from songs that merely contain synthesizers.

Alternative

Use historical alternative/college rock context, including Modern Rock chart evidence from 1988 onward and credible genre references.

Do not assume that all rock songs qualify as Alternative.

Evidence hierarchy

Prefer:

1. Contemporary music publications and reviews.
2. Established music reference sources.
3. Historical genre documentation.
4. Multiple independent sources agreeing on classification.

If classification is uncertain, retain the song with a disputed or probable designation rather than silently excluding it.

Keep genre evidence separate from chart evidence.

⸻

7. Deduplication and normalization

Deduplication must be careful and auditable.

* Normalize capitalization and whitespace.
* Standardize obvious punctuation variations.
* Preserve original source titles and artist names.
* Match songs using title, artist, and recording/version identity.
* Do not merge different artists’ recordings of the same song.
* Do not merge remixes or alternate recordings without evidence they are the same charted release.
* Keep a record of every merge decision.

Produce a separate deduplication_log.csv documenting:

* Original records
* Resulting song ID
* Reason for merging or keeping separate
* Confidence
* Manual review status

⸻

8. Validation and quality control

Before declaring the dataset complete:

* Verify every year from 1980 through 1989 is represented for each claimed chart.
* Check that chart dates and positions are valid.
* Identify missing chart issues.
* Check for duplicate weekly records.
* Verify song identities across charts.
* Compare samples against original chart publications.
* Report the number of raw chart appearances.
* Report the number of unique songs.
* Report the number of unique songs in each genre.
* Report overlap between genres.
* Report the number of songs appearing on multiple charts.

Do not present estimates as actual counts.

If a chart has incomplete coverage, show its actual coverage and exclude it from any claim of comprehensive results.

⸻

9. Required deliverables

Create a project directory containing:

1980s-music-chart-dataset/
    README.md
    sources.csv
    chart_appearances.csv
    songs.csv
    genre_classifications.csv
    deduplication_log.csv
    coverage_report.csv
    validation_report.md
    raw/
        [original downloaded source files]
    scripts/
        [reproducible acquisition and processing scripts]

README.md

Explain:

* Project scope
* Included charts
* Genre methodology
* Data schema
* How to reproduce the dataset
* Known limitations
* How to interpret the counts

coverage_report.csv

One row per chart and year, including:

* Chart
* Year
* Expected issues, if known
* Retrieved issues
* Missing issues
* Record count
* Coverage status

validation_report.md

Summarize:

* Total chart appearances
* Total unique songs
* Unique songs per genre
* Cross-genre overlap
* Cross-chart overlap
* Missing or incomplete data
* Validation checks performed
* Remaining uncertainties

⸻

10. Final acceptance criteria

The work is not complete unless:

1. The underlying chart records are actually populated.
2. The source of each record is traceable.
3. The dataset distinguishes weekly appearances from unique songs.
4. Genre classifications have supporting evidence.
5. Incomplete sources are clearly identified.
6. The scripts can reproduce the normalized data from the raw inputs.
7. The final counts can be independently checked.

Do not generate an Apple Music playlist at this stage.

The next phase will use the validated unique-song catalog to create an Apple Music-importable playlist, preserving song title, artist, and version information.

⸻

Execution priorities

Work in this order:

1. Establish which source datasets can actually be obtained.
2. Acquire and preserve the raw data.
3. Populate chart appearances.
4. Normalize and deduplicate songs.
5. Research and apply genre classifications.
6. Validate coverage and counts.
7. Produce the reports and deliverables.

If a source is inaccessible, report the blocker and continue with the sources that are available. Never fill gaps with guessed data.

The priority is a defensible dataset, not a large-looking song list.
