## Github Action Runner OS images

This is an experimental image which can be used as the OS image for the
[microvm-action-runner][mar].

I have only made an ARM image so far because that is what I needed for a demo.

In the future this builder should create multi-arch images for multiple ubuntu
versions. (And this image should also be versioned properly).

[This script][script] can be used with this image to register a microvm as a
runner:

[mar]: https://github.com/weaveworks-liquidmetal/microvm-action-runner
[script]: https://github.com/weaveworks-liquidmetal/microvm-action-runner/blob/main/pkg/microvm/userdata.sh
