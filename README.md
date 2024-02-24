# WIP

## references

- https://developer.mozilla.org/en-US/docs/Web/API/WebTransport
- https://github.com/BiagioFesta/wtransport / https://docs.rs/wtransport/latest/wtransport/
- https://stackoverflow.com/questions/77447254/got-opening-handshake-failed-when-trying-to-run-webtransport-samples-locally

## first attempt with wtransport

**STUCK**: I am having issues with chrome accepting the serverCertificateHashes object I computed.


create cert 1

    openssl req -newkey rsa:2048 -nodes -keyout certificate-key.pem \
    -x509 -days 10 -out certificate.pem -subj '/CN=Test Certificate' \
    -addext "subjectAltName = DNS:localhost"

create cert 2

    mkcert -install
    mkcert -CAROOT
    mkcert localhost localhost ::1

    mkcert -ecdsa -install
    mkcert -ecdsa -days 10 -cert-file localhost.crt -key-file localhost.key localhost 127.0.0.1 ::1

Compute base64 hash of public key with:

    openssl x509 -pubkey -noout -in certificate.pem | openssl rsa -pubin -outform der | openssl dgst -sha256 -binary | base64

    openssl x509 -in certificate.pem | openssl dgst -sha256 -binary | openssl enc -base64

host the index.html page:

    http-server . -p 8080 -c-1 --cors -S -C certificate.pem -K certificate-key.pem &

chrome:

    /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
    --origin-to-force-quic-on=localhost:4433 \
    localhost:8080 \
    --allow-running-insecure-content \
    --disable-proxy-certificate-handler \
    --allow-insecure-localhost \
    --ignore-certificate-errors-spki-list=cUEiyS4ciHN/pk1vdSVsnXQ97EHz+iy7A4Qob9Lir3U=


# alternatives to test

ambient https://github.com/AmbientRun/Ambient/blob/main/Cargo.toml
seems to be using this instead:
https://github.com/hyperium/h3/blob/master/examples/webtransport_server.rs
