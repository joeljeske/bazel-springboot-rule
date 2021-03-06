#
# Copyright (c) 2017-2021, salesforce.com, inc.
# All rights reserved.
# Licensed under the BSD 3-Clause license.
# For full license text, see LICENSE.txt file in the repo root  or https://opensource.org/licenses/BSD-3-Clause
#

# load our Spring Boot rule
load("//tools/springboot:springboot.bzl", "springboot")

# dependencies from other packages in the workspace
lib_deps = [
    "//samples/helloworld/libs/lib1",
    "//samples/helloworld/libs/lib2",
]

# create our deps list for Spring Boot
springboot_deps = [
    "//tools/springboot/import_bundles:springboot_required_deps",
    "@maven//:org_springframework_boot_spring_boot_starter_jetty",
    "@maven//:org_springframework_boot_spring_boot_starter_web",
    "@maven//:org_springframework_spring_webmvc",

    # bring in same dep again as above, but through a different maven_install
    # rule: the springboot rule does not package duplicate deps, first one wins
    "@spring_boot_starter_jetty//:org_springframework_boot_spring_boot_starter_jetty",
]

# This Java library contains the app code
java_library(
    name = "helloworld_lib",
    srcs = glob(["src/main/java/**/*.java"]),
    resources = glob(["src/main/resources/**"]),
    deps = springboot_deps + lib_deps,
)

test_deps = [
    "@maven//:junit_junit",
    "@maven//:org_hamcrest_hamcrest_core",
]

java_test(
   name = "SampleRestUnitTest",
   srcs = ["src/test/java/com/sample/SampleRestUnitTest.java"],
   deps = [ ":helloworld_lib" ] + test_deps,
)

# Build the app as a Spring Boot executable jar
springboot(
    name = "helloworld",
    boot_app_class = "com.sample.SampleMain",
    java_library = ":helloworld_lib",

    # DEPS ARE OPTIONAL HERE
    #  The springboot rule inherits all deps and runtime_deps from the java_library
    # deps = [],

    # TO TEST THE DUPE CLASSES FEATURE:
    #   There is an intentionally duplicated class in lib1 and lib2. Do this:
    #   1. set fail_on_duplicate_classes = True
    #   2. comment out lib1 or lib2 in helloworld_dupeclass_allowlist.txt
    #   Build should fail due to the duplicate class.
    fail_on_duplicate_classes = True,
    duplicate_class_allowlist = "helloworld_dupeclass_allowlist.txt",

    # Specify optional JVM args to use when the application is launched with 'bazel run'
    jvm_flags = "-Dcustomprop=gold",

    # data files can be made available in the working directory for when the app is launched with bazel run
    data = ["example_data.txt"],

    # if you have conflicting classes in dependency jar files, you can define the order in which the jars are loaded
    #  https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-executable-jar-format.html#executable-jar-war-index-files-classpath
    classpath_index = "helloworld_classpath.idx",
)

springboottest_deps = [
    "@maven//:org_springframework_spring_beans",
    "@maven//:org_springframework_boot_spring_boot_test",
    "@maven//:org_springframework_spring_test",
]

java_test(
    name = "SampleRestFuncTest",
    srcs = ["src/test/java/com/sample/SampleRestFuncTest.java"],
    deps = [ ":helloworld_lib" ] + test_deps + springboottest_deps,
    resources = glob(["src/test/resources/**"]),
)
