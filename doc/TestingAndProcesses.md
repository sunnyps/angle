# Handling Test Failures

ANGLE is tested by a number of test suites, including by the ANGLE CI and Try testers, and by the
Chromium FYI testers.  See pointers and explanations in the
[ANGLE Wrangling](https://chromium.googlesource.com/angle/angle/+/refs/heads/main/infra/ANGLEWrangling.md) documentation.

We run a large number of tests for each ANGLE CL, both in ANGLE standalone and Chromium
configurations, both pre- and post- commit.  Some tests will fail, crash, or timeout.  If these
cannot be addressed in a timely manner, file a bug and update test expectations files.  Timeliness
depends on the context.  For example, a Wrangler trying to unblock an AutoRoller will typically
suppress failures immediately; where a developer will typically delay landing their CL in favor of
fixing test failures.

## Handling a Vulkan Validation Layer error

Many tests are run with an option that enables the Vulkan Validation Layers (sometimes referred to
as VVL).  Validation errors will cause an otherwise-passing test to fail.

The [vulkan-deps into ANGLE AutoRoller](https://autoroll.skia.org/r/vulkan-deps-angle-autoroll)
updates ANGLE to the top-of-tree (ToT) upstream Vulkan tools and SDK.  Sometimes validation errors
are the result of bugs in the Vulkan Validation Layers, sometimes because of bugs in ANGLE.
Therefore, investigate the cause of the error and determine if it's an ANGLE bug or a Vulkan
Validation Layer bug.  For Vulkan Validation Layer bugs, file an
[upstream bug](https://github.com/KhronosGroup/Vulkan-ValidationLayers/issues/new), and
suppress the error.  The ANGLE Wrangler will also suppress a validation error when the
`vulkan-deps` AutoRoller introduces a new validation error.  The ANGLE Wrangler isn't expected to
resolve the error or diagnose an upstream bug (but it is welcome as extra credit).

Handle a validation error by doing the following:

1. [File an ANGLE bug](http://anglebug.com/new). If this is an active Wrangler issue, set the Label
   `Hotlist-Wrangler` on the bug.
2. Add the VVL error tag to the
   [kSkippedMessages](https://chromium.googlesource.com/angle/angle.git/+/8f8ca06dfb903fcc8517c69142c46c05e618f40d/src/libANGLE/renderer/vulkan/RendererVk.cpp#129)
   array in `RendererVk.cpp` file.  Follow the pattern for adding a comment with the associated bug
   in the line above the VVL tag.


## dEQP test expectations

There are a set of [dEQP](dEQP.md) expectations files in the
[src/tests/deqp_support](../src/tests/deqp_support) directory.  Notice the format of a line and
your choices for OS, driver, etc.  This is described in the directory's
[README.md](../src/tests/deqp_support/README.md) file.  This includes:
- `FAIL` - For a test that flakes often or persistently fails
- `SKIP` - For a test that crashes
- `TIMEOUT` - For a test that is very slow and may timeout


## angle_end2end_tests expectations

These expectations all live in the
[angle_end2end_tests_expectations.txt](../src/tests/angle_end2end_tests_expectations.txt) file.  The file format
is the same as for the dEQP expectations.  However, `FAIL` is not valid, and so the choices are:
- `SKIP` - For a test that fails or crashes
- `TIMEOUT` - For a test that is very slow and may timeout


## WebGL conformance test expectations

The expectations files are hosted in the `chromium/src` repository under
[content/test/gpu/gpu_tests/test_expectations](https://chromium.googlesource.com/chromium/src/+/refs/heads/main/content/test/gpu/gpu_tests/test_expectations/).
Note that this is not included in local ANGLE-only source tree.

The format of the file, including the different tags, is documented at the top of the file.  This
includes the following results:

- `RetryOnFailure` - For a test that rarely flakes
- `Failure` - For a test that fails consistently or flakes often
- `Skip` - For a test that causes catastrophic failures (e.g. ends an entire test run, causes a bot
   to BSoD); `Skip` should be used very sparingly

You will need to contact an OWNER of the file to +1 your CL.

You have two options for creating a CL to the expectations files:

1. For trivial edits, you can edit the expectations files via
   [Chromium Code Search](https://source.chromium.org/chromium/chromium/src/+/main:content/test/gpu/gpu_tests/test_expectations/):
    - In the browser, press the `Edit code` button.  This will bring up a new browser window/tab,
      in an editor mode.
    - Edit the expecations and then press the `Create change` (or `Update change` button if you
      need to change your CL later), which will create a CL.
2. Otherwise please [check out the code](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/get_the_code.md)
   and [upload a CL](https://chromium.googlesource.com/chromium/src/+/refs/heads/main/docs/contributing.md#Creating-a-change)
