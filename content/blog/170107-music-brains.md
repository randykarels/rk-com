---
title: Music APIs
subtitle: WIP notes about various music data APIs
date: 2017-01-01
template: plainwhite.html
slug: music-apis
---

Available APIs ([comparison](http://atchai.com/blog/2016-05-03-in-search-of-the-perfect-music-dataset/):

* [musicbrainz.org](http://musicbrains.org) API. Open and community managed.
* [Discogs](https://www.discogs.com/developers/). Very thorough with a range of data quality. Open API run by private company.
* [Google Knowledge Graph Search API](https://developers.google.com/knowledge-graph/?hl=en)
* [Rovi](http://developer.rovicorp.com/). Used by Apple/Allmusic?
* [Wikipedia](http://wikipedia.org)

Current Approach:

1. Use Google to get the top hit for a given search at MusicBrainz and Discogs.
2. Use MusicBrainz and Discogs API endpoints depending on results of Step 1
3. AllMusic? Do they have an API? Simple web scrape?
4. Wikipedia, how to get structured data back? Can I get that from the Google Knowledge Graph?

MusicBrainz "release-groups" roughly equivalent to Discogs Master releases. Discogs is much more comprehensive for vinyl releases.

Known Unknowns:

* Allmusic rankings and recommended albums
* Cover art

## musicbrainz.org

### Basic information about an album

Hit the `release` endpoint, and add the `inc` parameter to also include data about the artist, label, and the medium.

    ::bash
    $ http musicbrainz.org/ws/2/release/<mbid> fmt==json inc=='artists+labels+discids+url-rels'

Where `<mbid>` is the MusicBrainz id.

Example for Blood on the Tracks, ([json](http://musicbrainz.org/ws/2/release/ee7f79c0-f52b-45e5-800e-daa8e1d69144?fmt=json&inc=artists%2Blabels%2Bdiscids%2Burl-rels),
[web](http://musicbrainz.org/release/ee7f79c0-f52b-45e5-800e-daa8e1d69144))
