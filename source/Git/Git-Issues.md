# Git Issues

## Issues with push

    error: RPC failed; HTTP 400 curl 22 The requested URL returned error: 400
    fatal: the remote end hung up unexpectedly

Try to increase POST buffer

    git config --global http.postBuffer 157286400

