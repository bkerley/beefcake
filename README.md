# Beecake
## Protocol buffers, now with honey and painful stings.

Beecake is a fork of the Beefcake gem by Blake Mizerany, aimed at making it
quicker, easier, and more fun to talk to Riak and other services with similar
Protocol Buffers implementations.

# The Dream

```ruby
    require 'beecake'

    Beecake.load(
                 namespace=Riak::Client::BeecakeProtobuffsBackend,
                 directory='vendor/riak_pb/src',
                 file='riak_kv.proto'
                 )

    module Riak
      module Client
        module BeefcakeProtobuffsBackend
          class RpbBucketProps
            
            # override any methods
            def precommit=(newval)
              @precommit = newval
              @has_precommit = !!newval
            end
          end
        end
      end
    end

# Install

    $ gem install beecake

# Example

    require 'beecake'

    class Variety
      include Beecake::Message

      # Required
      required :x, :int32, 1
      required :y, :int32, 2

      # Optional
      optional :tag, :string, 3

      # Repeated
      repeated :ary,  :fixed64, 4
      repeated :pary, :fixed64, 5, :packed => true

      # Enums - Simply use a Module (NOTE: defaults are optional)
      module Foonum
        A = 1
        B = 2
      end

      # As per the spec, defaults are only set at the end
      # of decoding a message, not on object creation.
      optional :foo, Foonum, 6, :default => Foonum::B
    end

    x = Variety.new :x => 1, :y => 2
    # or
    x = Variety.new
    x.x = 1
    x.y = 2

## Encoding

Any object responding to `<<` can accept encoding

    s = ""
    x.encode(s)
    p [:s, s]
    # or (because encode returns the string/stream)
    p [:s, x.encode]
    # or
    open("x.dat") do |f|
      x.encode(f)
    end

    # decode
    encoded = x.encode
    decoded = Variety.decode(encoded)
    p [:x, decoded]

    # decode merge
    Variety.decoded(more_data, decoded)

# Why?

  Ruby deserves and needs first-class ProtoBuf support.
  Other libs didn't feel very "Ruby" to me and were hard to parse.

# Generate code from `.proto` file

    $ protoc --beecake_out output/path -I path/to/proto/files/dir path/to/proto/file

You can set the BEECAKE_NAMESPACE variable to generate the classes under a
desired namespace. (i.e. App::Foo::Bar)

# Dev

Source:

    $ git clone https://github.com/basho_labs/beecake.git

## Testing:

    $ rake test

## VMs:

Currently Beefcake is tested and working on:

* Ruby 2.0.0

## Support Features

* Optional fields
* Required fields
* Repeated fields
* Packed Repeated Fields
* Varint fields
* 32-bit fields
* 64-bit fields
* Length delemited fields
* Embeded Messages
* Unknown fields are ignored (as per spec)
* Enums
* Defaults (i.e. `optional :foo, :string, :default => "bar"`)

## Future

* Imports
* Use package in generation
* Groups (would be nice for accessing older protos)

# Further Reading

http://code.google.com/apis/protocolbuffers/docs/encoding.html

# Thank You

Blake Mizerany (bmizerany) for the original gem.
Keith Rarick (kr) for help with encoding/decoding.
Aman Gupta (tmm1) for help with cross VM support and performance enhancements.
