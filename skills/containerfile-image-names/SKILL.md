---
name: containerfile-image-names
description: Review Containerfile or Dockerfile FROM instructions for image names without an explicit registry. Use when asked to check base-image names or short-name resolution in a container build file.
license: Apache-2.0
metadata:
  version: "0.1.1"
---

# Review Containerfile image names

Read the requested Containerfile or Dockerfile without changing it or running a
build. For each FROM instruction, identify its image operand after any flags.

- Treat scratch as the empty base, not a registry image.
- Track AS stage names. A later FROM referring to an earlier stage is not an
  external image and must not be reported as a short image name.
- An external image has an explicit registry when it contains a slash and the
  component before that slash contains a dot or colon, or is localhost. A tag alone does not count:
  alpine:3.22 and library/alpine are short names; quay.io/team/app:1 is qualified.
- If the image contains a variable, resolve it only when its value is established
  by the provided build context. Otherwise report it as unresolved, not as a
  confirmed short name. Do not assume an ARG default overrides a supplied value.
- Report the file, line number, and image reference for each short or unresolved
  name. Explain that short names depend on registry configuration; ask which
  registry is intended rather than automatically inserting docker.io.

If no short or unresolved external references remain, state that explicitly.
Finish with counts of short names and unresolved references, keeping the two
categories separate so an unknown build argument is not reported as a defect.
