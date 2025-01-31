# Web API

Web API endpoints listed below have been implemented at this moment:

| ENDPOINT                                        | COMPATIBLE WITH MIRAKURUN? |
|-------------------------------------------------|----------------------------|
| [/api/version]                                  | :heavy_check_mark:         |
| [/api/status]                                   |                            |
| [/api/channels]                                 | :heavy_check_mark:         |
| [/api/channels/{channel_type}/{channel}/stream] | :heavy_check_mark:         |
| [/api/channels/{channel_type}/{channel}/services/{sid}/stream] |             |
| [/api/services]                                 | :heavy_check_mark:         |
| [/api/services/{id}]                            | :heavy_check_mark:         |
| [/api/services/{id}/logo]                       | :heavy_check_mark:         |
| [/api/services/{id}/stream]                     | :heavy_check_mark:         |
| [/api/programs]                                 | :heavy_check_mark:         |
| [/api/programs/{id}]                            | :heavy_check_mark:         |
| [/api/programs/{id}/stream]                     | :heavy_check_mark:         |
| [/api/tuners]                                   | :heavy_check_mark:         |
| [/api/docs]                                     | :heavy_check_mark:         |
| [/api/iptv/playlist]                            | :heavy_check_mark:         |
| [/api/iptv/channel.m3u8]                        |                            |
| [/api/iptv/epg]                                 |                            |
| [/api/iptv/xmltv]                               | :heavy_check_mark:         |
| [/api/timeshift]                                |                            |
| [/api/timeshift/{recorder}]                     |                            |
| [/api/timeshift/{recorder}/records]             |                            |
| [/api/timeshift/{recorder}/records/{record}]    |                            |
| [/api/timeshift/{recorder}/stream]              |                            |
| [/api/timeshift/{recorder}/records/{record}/stream]|                         |

The endpoints above are enough to run [EPGStation].

It also enough to run [BonDriver_mirakc].  It's strongly recommended to
enable `SERVICE_SPLIT` in `BonDriver_mirakc.ini` in order to reduce network
traffic between mirakc and BonDriver_mirakc.  Because the
`/api/channels/{channel_type}/{channel}/stream` endpoint provides a TS stream
which includes all services in the specified channel.

Unfortunately, mirakc doesn't work with [BonDriver_Mirakurun] at this point due
to some kind of issue in BonDriver_Mirakurun or mirakc.
See [issues/4](https://github.com/mirakc/mirakc/issues/4) for details
(discussion in Japanese).

Web API endpoints listed below have been implemented as the mirakc extensions:

* [/api/iptv/playlist]
* [/api/timeshift]
* [/api/timeshift/{recorder}]
* [/api/timeshift/{recorder}/records]
* [/api/timeshift/{recorder}/records/{record}]
* [/api/timeshift/{recorder}/stream]
* [/api/timeshift/{recorder}/records/{record}/stream]

[/api/version]: #apiversion
[/api/status]: #apistatus
[/api/channels]: #apichannels
[/api/channels/{channel_type}/{channel}/stream]: #apichannelschannel_typechannelstream
[/api/channels/{channel_type}/{channel}/services/{sid}/stream]: #apichannelschannel_typechannelservicessidstream
[/api/services]: #apiservices
[/api/services/{id}]: #apiservicesid
[/api/services/{id}/logo]: #apiservicesidlogo
[/api/services/{id}/stream]: #apiservicesidstream
[/api/programs]: #apiprograms
[/api/programs/{id}]: #apiprogramsid
[/api/programs/{id}/stream]: #apiprogramsidstream
[/api/tuners]: #apituners
[/api/docs]: #apidocs
[/api/iptv/playlist]: #apiiptvplaylist
[/api/iptv/channel.m3u8]: #apiiptvchannelm3u8
[/api/iptv/epg]: #apiiptvepg
[/api/iptv/xmltv]: #apiiptvxmltv
[/api/timeshift]: #apitimeshift
[/api/timeshift/{recorder}]: #apitimeshiftrecorder
[/api/timeshift/{recorder}/records]: #apitimeshiftrecorderrecords
[/api/timeshift/{recorder}/records/{record}]: #apitimeshiftrecorderrecordsrecord
[/api/timeshift/{recorder}/stream]: #apitimeshiftrecorderstream
[/api/timeshift/{recorder}/records/{record}/stream]: #apitimeshiftrecorderrecordsrecordstream

## Incompatibility of the `X-Mirakurun-Priority` header

There are the following differences of the `X-Mirakurun-Priority` header between
mirakc and Mirakurun:

* Treated as 0 when the minimum value of `X-Mirakurun-Priority` headers is less
  than 0
* Treaded as 128 when the maximum value of `X-Mirakurun-Priority` headers is
  greater than 0
* Can grab a tuner which is used by other users regardless of their priorities
  if the priority is 128

## Incompatibility of the `decode` query parameter

Before `1.0.30`, mirakc does **NOT** decode the stream when no `decode` query
parameter is specified.  This behavior is **incompatible** with Mirakurun.

This incompatibility was fixed in `1.0.30`.

## /api/version

Returns the **current** version in the same JSON format as Mirakurun.

The `latest` property is not supported and shows the current version.

## /api/status

Returns an empty object.

## /api/channels

Returns a list of channels.

Query parameters have **NOT** been supported.

## /api/channels/{channel_type}/{channel}/stream

Starts streaming for a channel.

## /api/channels/{channel_type}/{channel}/services/{sid}/stream

Starts streaming for a service in a channel.

Unlike Mirakurun, the `sid` must be a service ID.  In Mirakurun, the `sid` is a
service ID or the ID of a `ServiceItem` class.

## /api/services

Returns a list of services.

Query parameters have **NOT** been supported.

## /api/services/{id}

Returns a service.

## /api/services/{id}/logo

Returns a logo image if available.

Support GET and HEAD methods so that IPTV Simple Client in Kodi works properly.

## /api/services/{id}/stream

Starts streaming for a service.

Support GET and HEAD methods so that IPTV Simple Client in Kodi works well.

## /api/programs

Returns a list of programs.

Query parameters have **NOT** been supported.

## /api/programs/{id}

Returns a program.

## /api/programs/{id}/stream

Starts streaming for a program.

The streaming will start when the program starts and stops when the program
ends.

## /api/tuners

Returns a list of tuners.

Query parameters have **NOT** been supported.

## /api/docs

Returns a Swagger JSON data extracted from a Mirakurun by using the following
command:

```shell
./scripts/mirakurun-openapi-json -c -w 10 $MIRAKURUN_VERSION | \
  ./scripts/fixup-openapi-json >/etc/mirakurun.openapi.json
```

See also [issues/13](https://github.com/mirakc/mirakc/issues/13).

## Web API endpoints for IPTV clients

Using these endpoints, you can integrate mirakc with IPTV clients which support
the M3U8 playlist and the XMLTV document.

For example, you can integrate mirakc with PVR IPTV Simple Client in Jodi with
the following settings:

* General | M3U Play List URL
  * `http://<host>:<port>/api/iptv/playlist`
* EPG Settings | XMLTV URL
  * `http://<host>:<port>/api/iptv/epg`

After rebooting the Kodi, the following features will be available:

* TV
  * Channels
  * Guide
* Radio (if channels defined in `config.yml` have audio-only services)
  * Channels
  * Guide

### /api/iptv/playlist

Returns a M3U8 playlist which includes all TV services.

The format of the M3U8 playlist is compatible with EPGStation.

The following query parameters can be specified:

* pre-filters
* post-filters

The specified query parameters are added to URLs in the playlist.

### /api/iptv/channel.m3u8

Alias of [/api/iptv/playlist].  Added for compatibility with EPGStation.

### /api/iptv/epg

Returns a XMLTV document which contains TV program information for a specified
number of days.

The following query parameters can be specified:

* days (1-8, default: 3)

[EPGStation]: https://github.com/l3tnun/EPGStation
[BonDriver_mirakc]: https://github.com/epgdatacapbon/BonDriver_mirakc

### /api/iptv/xmltv

Alias of [/api/iptv/epg].  Added for compatibility with Mirakurun.

Unlike `/api/iptv/epg`, this endpoint does not support the `days` query
parameter for compatibility with Mirakurun and returns all programs.

## Web API endpoints for timeshift recording and playback

### /api/timeshift

Returns a list of timeshift recorders.

### /api/timeshift/{recorder}

Returns a timeshift recorder.

### /api/timeshift/{recorder}/records

Returns a list of records in a timeshift recorder.

### /api/timeshift/{recorder}/records/{record}

Returns a records in a timeshift recorder.

### /api/timeshift/{recorder}/stream

Starts live streaming for a timeshift recorder.

The following command starts live streaming from a specific record:

```
curl -sG http://mirakc:40772/api/timeshift/etv/stream?record=1
```

You can specify pre-filters and post-filters like any other endpoint for streaming.

### /api/timeshift/{recorder}/records/{record}/stream

Starts on-demand streaming for a record in a timeshift recorder.

The following command starts on-demand streaming from a specific record:

```
curl -sG http://mirakc:40772/api/timeshift/etv/records/1/stream
```

You can specify pre-filters and post-filters like any other endpoint for streaming.
You cannot seek the stream when you specify post-filters.
