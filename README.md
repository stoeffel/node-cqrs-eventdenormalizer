# Introduction

[![travis](https://img.shields.io/travis/adrai/node-cqrs-eventdenormalizer.svg)](https://travis-ci.org/adrai/node-cqrs-eventdenormalizer) [![npm](https://img.shields.io/npm/v/cqrs-eventdenormalizer.svg)](https://npmjs.org/package/cqrs-eventdenormalizer)

Node-cqrs-eventdenormalizer is a node.js module that implements the cqrs pattern.
It can be very useful as eventdenormalizer component if you work with (d)ddd, cqrs, domain, host, etc.

# Installation

    $ npm install cqrs-eventdenormalizer

# Usage

## Initialization

	var eventDenormalizer = require('cqrs-eventdenormalizer').eventDenormalizer;

	eventDenormalizer.on('event', function(evt) {
        // send to clients
    });
    eventDenormalizer.initialize({
        viewBuildersPath: __dirname + '/viewBuilders',
        extendersPath: __dirname + '/eventExtenders',
        ignoreRevision: false,
        disableQueuing: false
    }, function(err) {

    });

    eventDenormalizer.denormalize({ id: 'msgId', event: 'dummyChanged', payload: { id: '23445' } }, function(err) {

    });

    // if revision checks are enabled
    eventDenormalizer.on('eventMissing', function(id, aggregateRevision, eventRevision, evt) {
        // request the appropriate missing events from domain...
    });

    // to replay
    eventDenormalizer.replay([] /* array of ordered events */, function(err) {});

    // to replay streamed
    eventDenormalizer.replayStreamed(function(replay, done) {

        replay(evt1);
        replay(evt2);
        replay(evt3);

        done(function(err) { });

    });

## Define ViewBuilders...

    var base = require('cqrs-eventdenormalizer').viewBuilderBase;

    module.exports = base.extend({

        events: [
            'dummied',
            {
                event: 'dummyCreated',
                method: 'create',
                viewModelId: 'payload.id'
            },
            {
                event: 'dummyChanged',
                method: 'update',
                payload: 'payload'
            },
            {
                event: 'dummyDeleted',
                method: 'delete'
            },
            'dummySpezi',
            'somethingFlushed',
            {
                event: 'versioned'
            },
            {
                event: 'versioned'
                version: 1
            }
        ],

        collectionName: 'dummies',

        dummied: function(data, vm, evt) {
        },
  
        dummySpezi: function(data, vm, evt) {
          vm.otherValue = 'value';
        },
  
        somethingFlushed: function(data, vm, evt) {
        },

        versioned: function(data, vm, evt) {
        },

        versioned_1: function(data, vm, evt) {
        }

    });

## Settings for a scalable solution

    {
        viewBuildersPath: __dirname + '/viewBuilders',
        extendersPath: __dirname + '/eventExtenders',
        eventQueue: { type: 'inMemory', collectionName: 'events' },
        repository: {
            type: 'mongoDb',
            dbName: 'mydb',
            timeout: 60 * 1000
        },
        revisionGuardStore: {
            type: 'mongoDb',
            dbName: 'mydb',
            collectionName: 'revisionguard',
            timeout: 60 * 1000//,
            //revisionStart: 1
        },
        ignoreRevision: false,
        disableQueuing: false,
        revisionGuardQueueTimeout: 3000,
        revisionGuardQueueTimeoutMaxLoops: 3
    }

See [tests](https://github.com/adrai/node-cqrs-eventdenormalizer/tree/master/test) for detailed information...


[Release notes](https://github.com/adrai/node-cqrs-eventdenormalizer/blob/master/releasenotes.md)


# License

Copyright (c) 2014 Adriano Raiano

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
