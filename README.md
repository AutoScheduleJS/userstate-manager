# User-state Manager

> Manage user-state

[![Greenkeeper badge](https://badges.greenkeeper.io/AutoScheduleJS/userstate-manager.svg)](https://greenkeeper.io/)
[![Maintainability](https://api.codeclimate.com/v1/badges/76612be1e3d40698fb86/maintainability)](https://codeclimate.com/github/AutoScheduleJS/userstate-manager/maintainability)
[![Test Coverage](https://api.codeclimate.com/v1/badges/76612be1e3d40698fb86/test_coverage)](https://codeclimate.com/github/AutoScheduleJS/userstate-manager/test_coverage)
[![Build Status](https://travis-ci.org/AutoScheduleJS/userstate-manager.svg?branch=master)](https://travis-ci.org/AutoScheduleJS/userstate-manager)

needs:
- app update state as tasks finished
- data-io strategy to save change locally or sync with Google Drive (use LokiJS - pull, merge, push; pouchDB hasen't serialize methods)
- fetch original state (main app responsibility)
- lifespan handle: docs can specify an expiration date. Needs can query for docs with an expiration date greater than the given one. Provider can provide docs with specified expiration date, and have a minimal start. Thus, lifespan isn't handle by userstate-manager.
- update and insert satisfaction have to use a specific needResource build for this query, updated with each update/insert handle.
- build this needResource with the first satisfied range.
- update satisfaction: query (like providers) can have non-waiting need. Proposal for non-waiting needs that are updated: if need's find gather docs, use them, else return satisfied. Next turn, a new query will spawn to satisfy needs, and update satisfaction will  be able to use docs.

flow dependency:
- mongo-like engine for simulation (LokiJS)

structure dependency:
- queries-scheduler:
  - potentiality
  - material
- queries-fn:
  - query

workflow:
promise -> config (start, end) -> queries (to get transformation) -> query + (potential/material) with needs
  -> promise ranges of possibilities
  or
  -> promise [ranges of possibilities, errors]
  or
  -> promise resolve to ranges of possibilites, reject to no resources error

operations:
- build state timeline and check query's need / output to construct potential ranges.
- check query's need that wait for this resource
- build available mask (space where this resource is available)
- check query's output that wait to be lacking (provider's query)
- build available mask (space where this resource is lacking)
- intersect masks


Error to handle:
```
userstateHandler([...queries])(query, [...otherPots], [...otherMats])
query = consumer
otherPots = [{start: 45, end: 49}]
otherMats = [{start: 0, end: 4
}]

"Error: Object is not a document stored in the collection
    at Collection.remove (webpack-internal:///1:5481:15)
    at needResource.docs.forEach.doc (webpack-internal:///9:65:49)
    at Array.forEach (<anonymous>)
    at handleDelete (webpack-internal:///9:65:23)
    at needResources.map.needResource (webpack-internal:///9:58:16)
    at Array.map (<anonymous>)
    at eval (webpack-internal:///9:52:26)
    at eval (webpack-internal:///0:2434:14)
    at eval (webpack-internal:///0:219:19)
    at Object.exports.handleOutputTransformations (webpack-internal:///9:14:120)
    at Object.exports.handleTransformations (webpack-internal:///9:5:20)
    at Object.exports.computeRangeSatisfaction (webpack-internal:///8:12:49)
    at eval (webpack-internal:///3:37:61)
    at newUserstateHandler (/home/tchevrin/Téléchargements/github/queries-scheduler/src/data-flows/scheduler.flow.ts:207:59)
    at R.unnest.queries.map.R.converge (/home/tchevrin/Téléchargements/github/queries-scheduler/src/data-flows/scheduler.flow.ts:237:24)
    at /home/tchevrin/Téléchargements/github/queries-scheduler/node_modules/ramda/src/converge.js:47:17"
```