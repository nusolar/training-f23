# Goals

Congratulations on completing Fall training! Now that you are familiar
with our telemetry system, let's go over some goals for the upcoming year.

> ### Project listing format
>
> The projects on this page are listed in the following format:
>
> __*Project name* ([⚠️  | ⭐️ | 🌱], 👥 [SIZE], 📅 [DURATION])__
> 
> A project's level of importance is given by:
> * ⚠️  ⇒  Very important, should be prioritized.
> * ⭐️ ⇒  Important, ought to  happen eventually.
> * 🌱 ⇒  Less critical and more free form, will depend on time and member interest.
>
> A rough estimate of the number of people that a project will require is given
> in the __👥 [SIZE]__ field. Similarly, a (very) rough estimate of a project's
> expected duration in given in the __📅 [DURATION]__ field.
>
> These estimates are __very__ rough and __not__ hard requirements. The true duration or
> amount of people working on a project may end up differing drastically from what
> is predicted here.

## Polishing the current system

The current telemetry system last year was only partly ready during
FGSP 2023; issues with internet connectivity at the track prevented
us from pushing last minute fixes to the Raspberry Pi. The following
items address aspects of the current system that either need fixing,
testing, or just some polish.

### *Basic testing and data collection* (⚠️ , 👥 4-12, 📅 3-4 weeks)
We need to test all the components in the current telemetry system with the
car driving. There are likely to be bugs that need fixing or things not quite
working as expected, and we need to ensure everything is functional before
parts of SC7s are cannibalized for SC8. This also presents an opportunity
to collect real data traces from the car for other projects
([Data mocking and CAN traffic replays](#data-mocking-and-can-traffic-replays----2-4--3-6-weeks) and
[Exploring time series data with machine learning](#exploring-time-series-data-with-machine-learning---1-5--3-18-weeks),
for example).

This project will likely coincide with the later parts of training and involve
the whole Software team, as it's good hands-on experience.

### *Alternative backend for base-station database* (⚠️ , 👥 1-3, 📅 2 weeks)
We currently use SQLite as the database of choice for telemetry data on the base-station.
To make this work with Grafana (which does not support SQLite out of the box), we use
the [Grafana SQLite Datasource](https://grafana.com/grafana/plugins/frser-sqlite-datasource/)
plugin. However, we've discovered that the plugin does not seem to support M2 Macs, and
so this project will require refactoring the relevant code to target Postgres
(or a similar supported SQL database). This project can also explore non-relational
databases like [InfluxDB](https://www.influxdata.com/) (a time-series database).

### *More space-efficient serialization in XBee communication* (⚠️ , 👥 1-3, 📅 2-4 weeks)
The Xbee modems we use (see [Digi XBee® SX 1-Watt 900 MHz RF](../hardware/xbee.md)) are
limited to sending a maximum of 256 bytes per packet. We are able to transfer larger
messages by splitting the message into chunks, but this introduces issues if chunks
are lost in transmission and is generally inefficient. The current serialization format
we use for XBee communication, JSON, is very inefficient for this purpose. This project
will explore alternative serialization formats, like
[MessagePack](https://msgpack.org/index.html) or something custom-made, to reduce the number
of bytes sent over radio and eliminate the need for buffered communication.

### *Improved Grafana dashboard* (⚠️ , 👥 2-5, 📅 4-12 weeks)
Our current Grafana dashboard is incomplete. This project will require adding
more panels for relevant signals (which is fairly straightforward) and may
explore how best to represent error and limit flags, as well as how to leverage
Grafana's more advanced features to, for example, issue an alert if a temperature
reading exceeds a safe value.

## New projects

### *Data mocking and CAN traffic replays* (⚠️ , 👥 2-4, 📅 3-6 weeks)
We will not always have access to live CAN data from the solar car during our
meetings. Initial work was done last year to provide virtual CAN data generation,
loosely based on previous CAN traffic. This project will expand upon this work
to introduce data mocking based more accurately on observed signals.

It would also be nice to be able to save CAN traffic and replay it at a
later time. We have done this sporadically and in a limited capacity in the past,
essentially writing CAN traffic to text files. It would be much better to store
this data in a well-organized database (perhaps hosted in the cloud; see
[Cloud hosted databases and dashboard](#cloud-hosted-databases-and-dashboard---2-4--3-6-weeks)),
with an easier way to save traces.

Having CAN data saved and readily accessible is a prerequisite for projects which
need to run tests on or analyze such data (like
[Rigorous, end-to-end testing](#rigorous-end-to-end-testing---2-5--6-18-weeks)
and
[Exploring time series data with machine learning](#exploring-time-series-data-with-machine-learning---1-5--3-18-weeks)).

### *Rigorous, end-to-end testing* (⭐️, 👥 2-5, 📅 6-18 weeks)
No matter how well thought out a complex piece of software is, it is ultimately likely
to have bugs or be error-prone if it isn't battle-tested. This is especially true for
our telemetry system, since automobiles are generally not friendly environments for
computers. At any point during operation, the CAN connection may be jostled loose,
an XBee might go out of range, the Raspberry Pi may momentarily lose power or be
completely fried by excessive current, etc.

In all these scenarios, we want predictable invariants to be upheld. For
example, if the Raspberry Pi loses power, the information on the onboard
database should contain all the traffic parsed before the power loss (within a
window of, say, a few seconds). Or, if the onboard XBee is jostled lose mid-transmission
and sends a corrupted packet, the base-station receiver code should not crash.

The best way to ensure our software is robust (excluding years of field operation),
is to write comprehensive tests. A common technique is end-to-end testing, which
tests a system holistically, attempting to accurately mimic real operation. This
project will involving incrementally creating test suites to validate our software.
Those working on this project may explore modeling the car's operation as a state
machine, where various effects (e.g. a cable being jostled lose) are represented
by state transitions.

### *Cloud hosted databases and dashboard* (⭐️, 👥 2-4, 📅 3-6 weeks)
Currently, all databases and dashboards are run locally—that is, on a member's
laptop or the Raspberry Pi. It would be nice to migrate parts of the system,
especially the code running on the base-station, to cloud environments, so
that base-station operations would never depend on someone's laptop running out
of battery, for example. Hosting our data on the cloud would also allow us to
share our dashboard on a dedicated website.

### *XBee cellular communication* (⭐️, 👥 3-6, 📅 4-12 weeks)
Currently, all communication between the solar car and the base-station occurs
over two radio transmitters. These transmitters work well enough, but are
prone to losing contact when separated too far. Last year, we purchased an
XBee cellular chip, but we have not started using it yet. This project will
involve working with a new piece of hardware to improve our system and will
likely involve working with cloud environments.

### *Collaborating with Electrical Team* (⭐️, 👥 1-2, 📅 TBD)
The Electrical team plans on adding new connections to the pins on the Raspberry
Pi, which will allow us to poll information from the headlights, horn, etc. We
need one or two people to work in liaison with the Electrical team to oversee
the addition of new hardware to our Pi.

### *Collaborating with Business Team* (🌱, 👥 1-2, 📅 TBD)
The Business team partially oversees NUSolar's public and internal websites.
Both could use some improvements, and we would be interested in integrating
our dashboard with the internal website.

### *Onboard GPS module with dashboard integration* (🌱, 👥 2-5, 📅 6-12 weeks)
It would be nice to visualize the car's progress along the track on our dashboard.
This project will oversee the addition of a new GPS module to the car, which will
provide positioning data that must be relayed to the base-station and displayed
visually.

### *Onboard front-facing camera with dashboard integration* (🌱, 👥 2-5, 📅 6-12 weeks)
A similar improvement to our dashboard would be a front-facing camera that shows
the drivers perspective in the car. F1 broadcasts, for example, tend to show this
perspective. While it would be a great addition to the dashboard, members working
on this project will need to first determine whether transmitting live video data
if feasible with our hardware.

### *Investigating the energy cost of the Raspberry Pi and Python runtime* (🌱, 👥 1-2, 📅 4-12 weeks)
Energy consumption is not a factor often considered when writing software. For our system,
however, being a battery hog is unacceptable, as all the power the onboard system uses
ultimately comes from the solar cells that power the car. Those interested in other
programming languages might investigate the energy cost of running Python on the
Raspberry Pi over a more lightweight language like C, Go, or Rust. If it turns out
the Python runtime is unacceptably inefficient for our needs (this admittedly seems
unlikely), the team could consider migrating a small part of the system to a different
language. The team may also explore the benefits (and possibility) of using a
microcontroller instead of the Raspberry Pi microprocessor.

### *Running an onboard REPL to aid debugging* (🌱, 👥 1-4, 📅 3-6 weeks)
LISP was the first programming language to feature a REPL (Read, Eval, Print Loop).
In the late 90s, a team at NASA deployed LISP code running with a REPL on a Deep
Space probe. When code on the probe stopped working, engineers on Earth were able
to debug it and send it the necessary commands to fix the issue through this REPL.

Python, like LISP, can run with a REPL, and, like the NASA team, we stand to
benefit from accessing such an onboard REPL remotely. It would be a challenge
to figure out to set this up over XBee devices, but this project could be a
lot of fun!

### *Exploring time series data with machine learning* (🌱, 👥 1-5, 📅 3-18 weeks)
Analyzing CAN traffic with machine learning models could provide interesting
opportunities for racing strategy. Through this project, members interested in ML
could explore how, if at all, we could use such models to optimize our decisions
at a race.

## Other projects

Have an idea for a project that's not listed here? That's great! As long as it's
even tangentially related to the solar car, you'll be able to work on it. Software
is fortunate to be free from many of the tight deadlines and requirements Mechanical
and Electrical face (especially true during this year's design cycle), and, as a
result, we tend to have more freedom to experiment.
