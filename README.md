# Apivore

Automatically tests your rails API against its Swagger description of end-points, models, and query parameters.

Currently supports and validates against Swagger 2.0, see https://github.com/swagger-api/swagger-spec/blob/master/versions/2.0.md

## Installation

Currently this gem is not yet on rubygems.org

Install from github using the following in your Gemfile:

    gem 'apivore', :github => 'hornc/apivore', :branch => 'master'

## Usage

Create a new request spec in spec/requests:
```ruby
require 'spec_helper'
require 'apivore'
include Apivore::RspecBuilder

describe "the API" do
  validate("api/swagger.json")
end
```
using the path to your application's Swagger 2.0 documentation in place of 'api/swagger.json'

This will validate the json against the Swagger 2.0 schema and the generate tests for each documented endpoint combination of a path, method, and expected response.

If your Swagger documentation contains a schema for the response model, the generated tests will test whether the response conforms to that model.

For paths that take parameters, listed in the Swagger docs like '/books/{id}.json', values need to be passed to Apivore to substitute in to access the responses generated by your test data.

This is accomplished using 'apivore_steup' blocks that contain the test setup code (to be run immediately before the rspec test is run) and return a hash of substitution parameters.
```ruby
apivore_setup '/books/{id}.json', 'get', '200' do
  book = FactoryGirl.create :book, title: 'One Test Book', description: "Setup your test data however you like, but return the relevant parameters so it can be accessed via the path"
  {'id' => book.id}
end
```
Run the tests as part of your normal rspec specs, e.g.,
	rake spec:requests

The generated tests will output details on where the Swagger documentation fails validation, or on any mismatch of expected responses from your documented endpoints, or where you returned API response does not match the documented model.

## Useful Resources

* http://json-schema.org/
* https://github.com/swagger-api/swagger-spec
* https://github.com/hoxworth/json-schema

## License

Copyright 2014 Westfield Labs Corporation

Licensed under the [Apache v2.0](http://www.apache.org/licenses/LICENSE-2.0.html) license.

This project includes and makes use of a modified version of the [Swagger 2.0 schema json](https://github.com/swagger-api/swagger-spec/blob/master/schemas/v2.0/schema.json) (Copyright 2014 Reverb Technologies, Inc. Released under the MIT license) included here as data/swagger_2.0_schema.json

The modifications involve explicitly including in the previously external references to http://json-schema.org/draft-04/schema to prevent network resource fetching and speed up validation times considerably.

## Contributors

* Charles Horn (https://github.com/hornc)
* Leon Dewey ()
