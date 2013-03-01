# Trans::Api

Trans::Api is an ruby implementation for Transmission RPC. Based on RPC spec 13328
https://trac.transmissionbt.com/browser/trunk/extras/rpc-spec.txt

It required for the Transmission RPC to run the 'remote access':

OSX:

  Transmission > Preferences > Remote (tab) > Enable remote access


### Platforms

This gem is (build and) tested with:

  OSX Lion, Mountain Lion

  Ruby 1.9.3

  Rails: 3.2.8, 3.2.9

  Transmission 2.73 (13589)


### Roadmap

<<<<<<< HEAD
* Version (0.0.1)

  Current version
=======
* Version (0.0.1) (current)

  Initial project import.
>>>>>>> Maintanance.

* Version (0.0.2)

  Session object include: 'blocklist', 'port-test'

<<<<<<< HEAD
=======
  Session singleton object

>>>>>>> Maintanance.
  Torrent object include 'torrent-start-now', 'queue-move-top/up/down/bottom'

  Torrent object 'delete_all!' explicit torrent references

<<<<<<< HEAD
  Torrent object 'waitfor_after' helper to check for lambda after calling it's chained cousin

  Torrent object 'waitfor_before' helper to check for lambda before calling it's chained cousin
=======
  Torrent object 'waitfor' helper to check for lambda after/before calling it's chained cousin

>>>>>>> Maintanance.


### Known Issues

The Transmission RPC call 'torrent-remove' (implemented as torrent.delete! and Torrent::delete_all!) will crash the daemon! This is NOT a known Transmission issue.


## Installation

Add this line to your application's Gemfile:

<<<<<<< HEAD
  gem 'trans-api', git: "git://github.com/dblommesteijn/trans-api.git"
=======
    gem 'trans-api', git: "git://github.com/dblommesteijn/trans-api.git"
>>>>>>> Maintanance.

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install trans-api


## Setup

Define a configuration for your connection (initialize script)

```ruby
CONFIG = { host: "localhost", port: 9091, user: "admin", pass: "admin", path: "/transmission/rpc" }
```

Setup default configuration (initialize script)

```ruby
Trans::Api::Client.config = CONFIG
```

Define default torrent fields (bulk requests)
NOTE: connection is slow when running many torrents with a large amount of fields (transmission rpc issue).
On requesting an additional info field from the torrent object, a new call is made to the RPC (and stored
withing the object).

```ruby
Trans::Api::Torrent.default_fields = [ :id, :status, :name ]
```

Example

```ruby
Trans::Api::Torrent.default_fields = [ :id, :status, :name ]
# loads the torrent object of id 1 with fields: :id, :status, :name
id = 1
torrent = Trans::Api::Torrent.find id
# calls the rpc to receive files from the defined torrent
torrent.files
```

## Examples

  Check the examples/ folder or Unit tests: test/unit/trans_ (session/torrent) _object.rb


## Usage

Trans api can be used in two ways:


1. Connect (raw class)

```ruby
tc = Trans::Api::Connect.new CONFIG
torrents = tc.torrent_get([:id, :name, :status])
```

2. Mapped objects (torrent, file, session classes)

	examples below.


### Requesting Torrent Info

Get all registered torrents

```ruby
Trans::Api::Torrent.all
```

Get a specific torrent by transmission id
NOTE: transmission assigns random ids to torrents on daemon start

```ruby
id = 1
Trans::Api::Torrent.find id
```

### Torrent static calls

Start all (start all transfers)

```ruby
Trans::Api::Torrent.start_all
```

Stop all (stop all transfers)

```ruby
Trans::Api::Torrent.stop_all
```

Delete all (tranmission daemon will crash on rapid call)

```ruby
torrents = Trans::Api::Torrent.all
# assign explicit torrent objects for removal
Trans::Api::Torrent.delete_all torrents
```

Add torrent file

```ruby
options = {paused: true}
Trans::Api::Torrent.add_file filename, options
```

Get all fields
```ruby
Trans::Api::Torrent.ACCESSOR_FIELDS
```

Get all Mutable fields
```ruby
Trans::Api::Torrent.MUTATOR_FIELDS
```


### Torrent instance actions

Get a torrent object

```ruby
id = 1
torrent = Trans::Api::Torrent.find id
```

Save (store changed values)

```ruby
torrent.save!
```

Start (activate torrent for transfer)

```ruby
torrent.start!
```

Start Now (not sure what's the difference to start!, it's a different API call)

```ruby
torrent.start_now!
```

Reset (reload all torrent fields, including later requested ones)

```ruby
torrent.reset!
```

Stop (stop torrent transfer)

```ruby
torrent.stop!
```

File Objects (returns a list of Trans::Api::File objects)

```ruby
torrent.files_objects
```

Status names (get the status name of the torrent)

```ruby
torrent.status_name
```

Verify (recheck downloaded files)

```ruby
torrent.verify!
```
<<<<<<< HEAD

Reannounce Torrent

```ruby
torrent.reannounce!
```

Delete (tranmission daemon will crash on rapid call)

```ruby
options = {delete_local_data: true}
torrent.delete! options
```

Waitfor (automatic delayed responce after/before chained method is called)

  Blocking busy waiting for lambda to return true

```ruby
=======

Reannounce Torrent

```ruby
torrent.reannounce!
```

Delete (tranmission daemon will crash on rapid call)

```ruby
options = {delete_local_data: true}
torrent.delete! options
```

Waitfor (automatic delayed responce after/before chained method is called)

  Blocking busy waiting for lambda to return true

```ruby
>>>>>>> Maintanance.
# waitfor status name not equals stopped after calling start!
optional = :after
torrent.waitfor( lambda{|t| t.status_name != :stopped}, optional ).start!
# waitfor status name equals stopped after calling stop!
torrent.waitfor( lambda{|t| t.status_name == :stopped}, optional ).stop!
# NOTE: waitfor can be used for blocking without a chained method (optional = :before only)
```

NOTE: defined torrent accessor fields are defined as instance methods to the Torrent object


### Session Info

Get session object (singleton)

```ruby
<<<<<<< HEAD
session = Trans::Api::Session.new
=======
session = Trans::Api::Session.instance
>>>>>>> Maintanance.
```

Get available fields (returns symbols of get/set fields)

```ruby
session.fields
```
<<<<<<< HEAD
=======

Get all fields and values

```ruby
session.fields_and_values
```

Reset (reload object, request information and not saving changes)

```ruby
session.reset!
```
>>>>>>> Maintanance.

NOTE: defined session fields are defined as instance methods to the Session object


### File Info

Getting files from a torrent (file cannot be used standalone, it's an helper class)

```ruby
id = 1
torrent = Trans::Api::Torrent.find id
files = torrent.files_objects
files.each do |file|
	# manipulate file here!
end
```

File name

```ruby
file.name
```

File mark unwant (mark for ignore, not download)

```ruby
file.unwant
```

File mark want (mark for download)

```ruby
file.want
```

File wanted? (marked for download)

```ruby
file.wanted?
```

File stats

```ruby
file.stat
```

NOTE: changed preferences (want, unwant) set options on the linked Torrent object, after saving torrent (torrent.save!) file mutations are stored.

