---
layout: posts
title: "Killing the flags: bazel with remote execution and caching"
authors:
  - ishikhman
---
tl;dr: How does one configure bazel build to work with a remote execution or remote cache? Historically, there've been quite a few flags to take care of, but not all of them should still be around. 

## What happened?

We've been working on improving a flag situation for a while, therefore the changes did not happen in one release unfortunately. Let's see when and what has changed or will change.

### bazel 0.26

Some small improvements happened over here.

#### Breaking changes:
* a long time ago deprecated flag `--remote_rest_cache` was ~~killed~~ deleted.

  This flag was used to specify a remote cache HTTP/HTTPs endpoint.
 
  **How to migrate**: use `--remote_cache` instead.

#### Backward compatible changes:

* `--remote_cache_proxy` renamed to `--remote_proxy`.

  This flag is used to connect to a remote cache via unix domain socket (see [docs](https://docs.bazel.build/versions/0.25.0/remote-caching.html#unix-sockets) for details).

  **How to migrate**: replace all your `--remote_cache_proxy` with `--remote_proxy`.

* `--remote_http_cache` renamed to `--remote_cache`.
  
  This flag is used to specify a remote cache HTTP/HTTPs endpoint (see [docs](https://docs.bazel.build/versions/0.25.0/remote-caching.html#run-bazel-using-the-remote-cache) for details).

  **How to migrate**: replace all your `--remote_http_cache` with `--remote_cache`.
  
*Note: flags `--remote_http_cache` and `--remote_cache_proxy` are now deprecated and will be removed at some point in the future.*


### bazel 0.27

#### Breaking changes:

* `--incompatible_list_based_execution_strategy_selection` flipped #7480
`--remote_local_fallback_strategy` flag is deprecated

 	=> consequences such as:
	
	- default behavior: remote,worker,sandboxed,local
	- sandboxed is not invoked if OS does not support sadnboxing (Windows)
	- no more fallbacks to implicit strategy - only to pre-defined ones
	- how to migrate
		- link or short description?


* `--tls_enabled` is now deprecated. 
use `--icompatible_tls_enabled_removed` flag to test 
https://github.com/bazelbuild/bazel/issues/8061

How to migrate

`--tls_enabled --remote_executor=remoteexecutor.url.com` 
=> `--remote_executor=remoteexecutor.url.com`
or `--remote_executor=grpcs://remoteexecutor.url.com`



	
	
Removed:	
 `--experimental_remote_retry`
 `--experimental_remote_retry_jitter` 
 `--experimental_remote_retry_max_attempts`
 `--experimental_remote_retry_max_delay_millis`
 `--experimental_remote_retry_multiplier`
 `--experimental_remote_retry_start_delay_millis`

use instead:
`--remote_retries=0` to disable
`--remote_retries=5` default
`--remote_retries=3` to customize



0.30
--tls-enabled removed

tags to be propagated to execution requirements (fixes a few Github issues #...)

simplify `--remote_default_platform_properties`


add also 'old' deprecated flag usages (like --auth_enabled)
