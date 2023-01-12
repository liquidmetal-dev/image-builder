ARG IMAGE_NAME
FROM ${IMAGE_NAME} AS builder
FROM scratch

COPY --from=builder /boot /boot
