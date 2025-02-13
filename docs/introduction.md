---
sidebar_position: 1
sidebar_label: Introduction
pagination_next: installation/linux
title: Introduction and features
description: Introduction, features and history of autobrr
keywords:
  [
    autobrr,
    indexers,
    trackers,
    torrents,
    bittorrent,
    racing,
    p2p,
    autodl-irssi,
    replacement,
    autodl replacement,
    autodl-irssi replacement,
    irssi,
    trackarr,
    flexget,
    irc,
    announce,
    torznab,
    prowlarr,
    rss,
    Golang,
    regex,
    docker,
    discord,
    telegram,
    notifiarr,
    qbittorrent,
    deluge,
    transmission,
    sonarr,
    radarr,
    lidarr,
    whisparr,
    webhook,
    cross-seed,
    seeding,
    x-seed,
    xseed,
    cabal,
    api,
  ]
---

autobrr is the modern download automation tool for torrents.

With inspiration and ideas from tools like trackarr, autodl-irssi and flexget we built one tool that can do it all, and then some.

As of right now, autobrr features:

- Support for 50+ trackers with IRC announces
- RSS and Torznab support via Prowlarr to easily get access to hundreds of trackers
- Powerful but simple filtering with RegEx support (like in autodl-irssi)
- Easy to use and mobile friendly web UI (with dark mode!) to manage everything
- Built on Go and React making autobrr lightweight and perfect for supporting multiple platforms (Linux, FreeBSD, Windows, macOS) on different architectures (e.g. x86, ARM)
- Great container support (Docker, k8s/Kubernetes)
- Database engine supporting both PostgreSQL and SQLite
- Notifications (Discord, Telegram, Notifiarr)
- One autobrr instance can communicate with multiple clients (both torrent and \*arr) on remote servers
- Base path / Subfolder (and subdomain) support for convenient reverse-proxy support

Available download clients and actions

- qBittorrent (with built in re-announce, categories, rules, max active downloads, etc)
- Deluge v1+ and v2+
- rTorrent / ruTorrent
- Transmission
- Sonarr, Radarr, Lidarr, Readarr and Whisparr (pushes releases directly to them and gets in the early swarm, instead of getting them via RSS when it's already over)
- Watch folder
- Exec custom scripts
- Webhook

## Planned features

- Automatic cross-seeding of existing releases

## About

The development of autobrr started in Early 2020, entering rapid development in Summer 2021 due to dissatisfaction with needing 3+ tools to do one job. Autobrr has since gained quite a bit of traction and has a growing community supporting the project.

Autobrr was developed with resource consumption in mind. The software uses API calls to reduce unnecessary downloads of .torrent files from sites like BTN, RED, PTP, and GGn. On other sites, it will download the .torrent only if the information is not present in the announce message.

## Quick Start

The following considers a common use case, feeding IRC announcements for a private
tracker to a Servarr instance, as a way to illustrate how the components of autobrr fit
together and what is required to get up and running.  It's not the only use case and
other use cases may require more RTFM'ing in [the configuration
docs](/configuration/autobrr).

1. [Install autobrr](/installation/linux).

	Proceed once the web UI is accessible.

2. Create a user for yourself.

	Most easily done through the initial "wizard" in the web UI when autobrr is first
	started.

3. [Register a nick on your indexer's IRC
   network](/configuration/irc#registering-with-nickserv).

4. [Group a "bot" nick with your real nick](/configuration/irc#grouping-nicks).

   This is the common case, but check your tracker's IRC documentation and adjust as
   appropriate.

5. Add an [indexer](/configuration/indexers).

6. Add a [download client](/configuration/download-clients#sonarr).

	**NOTE**: In the context of autobrr, Servarr instances are considered download
	clients.

7. Add a [filter](/filters).

	To feed all IRC announcements to a Servarr instance to let it decide what, if
	anything, to do with the release, just add a filter with the indexer from #5
	selected and leave the rest blank.  Don't forget to enable the filter.

	**NOTE**: Autobrr does nothing with received IRC announcements without at least one
	filter applied to at least one indexer.

8. Add a [filter action](/filters/actions).

	Add an action of the matching Servarr type (e.g. Sonarr type action for a Sonarr
	instance), select the "download client" that corresponds to that type from #6, give
	it a name and save.

	**NOTE**: Autobrr does nothing with received IRC announcements without at least one
	action enabled in at least one filter.

9. Double check the resulting settings so far.

	Review all the indexer, download client, and IRC network settings in the autobrr web
	UI and correct any errors and omissions.  Ensure that everything but the IRC network
	is enabled.

10. Enable the IRC network.

	**NOTE**: The network will display as `unhealthy` and `network unhealthy` messages
	appear in the logs until authentication has succeeded and autobrr has successfully
	joined the announcements channel.  So you may safely ignore these messages until the
	logs show further information about connecting, authenticating and joining the
	channel.

Now you can monitor the logs for announcements from IRC, pushes to the Servarr instance,
and details for what, if anything, Servarr did with the release:

```
INFO Matched 'Foo Series S01E01 1080p WEB h264-BarGrp' (All) for foo-indexer
DEBUG release.store: &{ID:12 FilterStatus:FILTER_APPROVED Rejections:[] Indexer:foo-indexer FilterName:All Protocol:torrent Implementation:IRC Timestamp:1970-01-01 00:00:00.000000000 -0000 UTC m=+0000.000000000 GroupID: TorrentID:######### TorrentURL:https://www.example.com/Foo+Series+S01E01+1080p+WEB+h264-BarGrp.torrent TorrentTmpFile: TorrentDataRawBytes:[] TorrentHash: TorrentName:Foo Series S01E01 1080p WEB h264-BarGrp Size:0 Title:Foo Series Category:TV :: Episodes HD Categories:[] Season:1 Episode:1 Year:0 Resolution:1080p Source:WEB Codec:[H.264] Container: HDR:[] Audio:[] AudioChannels: Group:BarGrp Region: Language: Proper:false Repack:false Website: Artists: Type: LogScore:0 IsScene:false Origin: Tags:[] ReleaseTags: Freeleech:false FreeleechPercent:0 Bonus:[] Uploader:Anonymous PreTime: Other:[] RawCookie: AdditionalSizeCheckRequired:false FilterID:1 Filter:0x########## ActionStatus:[]}
DEBUG sonarr: release push rejected: Foo Series S01E01 1080p WEB h264-BarGrp, indexer foo-indexer to http://localhost:8989 reasons: '[Unknown Series]'
DEBUG release rejected: Unknown Series
```
