# lambdacd-junit

A Clojure library to integrate Junit test reports in LambdaCD

[![Build Status](https://travis-ci.org/thilo11/lambdacd-junit.svg?branch=master)](https://travis-ci.org/thilo11/lambdacd-junit)

## Usage
* add `[lambdacd-junit "0.2.0"]` to your project dependencies 
* require lambdacd-junit 
`(:require [lambdacd-junit.core :as junit4])`
* and pass the LambdaCD context, the directory with the junit xml result files, and the test report file patterns to the junit4-reports function:
`(junit4/junit4-reports args "/build/test-results/" "Unit Tests" [#"TEST-.*"])`
cf. the tests in the core_test.clf file
* `junit4-reports` works in conjunction with the shell command in LambdaCD (see example below)
After triggering the junit tests you should see an item 'Junit test results' including the test results below the details section generated by LambdaCD. 
* To install lambdacd-junit locally clone this repository and do a `lein install`
* run the tests as usual with `lein test`
Note, lambdacd-junit works best with LambdaCD 0.7.1 upwards.

## Example 

to add unit and integration tests in a typical gradle based Java project you could do 

```clojure
(ns pipeline.steps
  (:require
    [clojure.java.io :as io]
    [lambdacd-git.core :as core]
    [lambdacd.steps.shell :as shell]
    [lambdacd-junit.core :as junit4]))
    
(def gradle-test-cmd "./gradlew clean test")

(def gradle-integration-test-cmd "./gradlew clean integrationTest")

(defn clone [args ctx]
  (core/clone ctx (:repo (:config ctx)) (:revision args) (:cwd args)))
  
(defn gradle-test [args ctx]
  (-> (shell/bash ctx (:cwd args) gradle-test-cmd)
      (junit4/junit4-reports args "/build/test-results/" "Unit Tests" [#"TEST-.*"])))

(defn gradle-integration-test [args ctx]
  (-> (shell/bash ctx (:cwd args) gradle-integration-test-cmd )
      (junit4/junit4-reports args "/build/test-results/" "Integration Tests" [#"TEST-.+IntegrationTest.*"])))
```

## Screenshot
![LambdaCD with test reports](assets/pipeline-example.png "Pipeline with Unit tests and a failing Integration test")

## Related projects

* [lambdacd](https://github.com/flosell/lambdacd): a library to define a continuous delivery pipeline in code

## License

Copyright © 2016, 2017 Thilo Horstmann

Distributed under the Apache License 2.0
