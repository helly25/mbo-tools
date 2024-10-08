# SPDX-FileCopyrightText: Copyright (c) The helly25/mbo authors (helly25.com)
# SPDX-License-Identifier: Apache-2.0
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

"""Tool to fetch and analyze CircleCI workflows."""

load("@aspect_rules_py//py:defs.bzl", "py_binary", "py_pex_binary")
load("@bazel_skylib//rules:diff_test.bzl", "diff_test")
load("@my_pip_deps//:requirements.bzl", "requirement")
load("@rules_python//python:defs.bzl", "py_library", "py_test")

package(default_visibility = ["//visibility:private"])

py_library(
    name = "circleci_api_v2_py",
    srcs = ["circleci_api_v2.py"],
    deps = [
        requirement("requests"),
        requirement("responses"),
    ],
)

py_test(
    name = "circleci_api_v2_test",
    srcs = ["circleci_api_v2_test.py"],
    deps = [
        ":circleci_api_v2_py",
        requirement("parameterized"),
        requirement("requests"),
    ],
)

py_library(
    name = "workflows_lib_py",
    srcs = ["workflows_lib.py"],
    deps = [
        ":circleci_api_v2_py",
        "//mbo/app:commands_py",
        "//mbo/app:flags_py",
        requirement("humanize"),
        requirement("pytimeparse"),
    ],
)

py_test(
    name = "workflows_lib_test",
    srcs = ["workflows_lib_test.py"],
    deps = [
        ":circleci_api_v2_py",
        ":workflows_lib_py",
        "//mbo/app:commands_py",
        requirement("parameterized"),
    ],
)

py_binary(
    name = "workflows_bin",
    testonly = True,
    srcs = ["workflows.py"],
    tags = ["manual"],
    visibility = ["//visibility:private"],
    deps = [
        ":workflows_lib_py",
        "//mbo/app:commands_py",
    ],
)

py_pex_binary(
    name = "workflows",
    testonly = True,
    binary = ":workflows_bin",
)

genrule(
    name = "readme_gen",
    testonly = True,
    srcs = ["//:README.header.txt"],
    outs = ["readme.txt"],
    cmd = """
        ./$(location :workflows) \
            --help_output_mode=markdown \
            help \
            --all_commands \
            --header_level=1 \
            --prefix="$(location //:README.header.txt)" \
            > "$@"
    """,
    tools = [":workflows"],
)

diff_test(
    name = "readme_test",
    file1 = "//:README.md",
    file2 = ":readme.txt",
)
