---
title: JavaScript
excerpt: ''
deprecated: false
hidden: true
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
Qase provides dedicated JavaScript reporters that make it simple to publish automated test results from popular JavaScript testing frameworks into your Qase workspace. Whether you are using frameworks like Mocha, Jest, Jasmine, or Cypress, Qase’s JavaScript reporters streamline the reporting process by automatically sending detailed execution results, improving traceability, and fostering better collaboration between QA and development teams.

<br />

## [qase-javascript-commons](https://github.com/qase-tms/qase-javascript/tree/main/qase-javascript-commons)

This module is an SDK for developing test reporters for Qase TMS. It’s using qaseio as an API client, and all Qase reporters are, in turn, using this package. You should use it if you’re developing your own test reporter for a special-purpose framework.

To report results from tests using a popular framework or test runner, don’t install this module directly and use the corresponding reporter module instead.