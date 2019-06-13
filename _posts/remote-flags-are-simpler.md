---
layout: posts
title: "Bazel Remote: Killing the flags"
authors:
  - ishikhman
---
tl;dr: How does one configure bazel build to work with a remote execution or remote cache? Historically, there've been quite a few flags to take care of, but not all of them should still be around. 

## What happened?

We've been working on improving a flag situation for a while, therefore the changes did not happen in one release but over a few releases and some of them are only planned to happen in the future releases. Let's see when and what has changed or will change.

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
	
	- The user can pass comma-separated lists of strategies to the strategy related flags: `--spawn_strategy=remote,worker,linux-sandbox`. 
	- default behavior: Use remote execution if it's available, otherwise persistent workers, otherwise sandboxed execution, otherwise non-sandboxed execution.
	- If action cannot be executed with any of the given strategies, build will fail. 
	
	See more details and *how to migrate* advice over here: [list based strategy](2019-06-27-list-strategy.md)

#### Backward compatible changes:

* `--tls_enabled` is now deprecated
* TLS is enabled by default for gRPC connections and hidden behind `--icompatible_tls_enabled_removed` flag

use `--icompatible_tls_enabled_removed` flag to test 
https://github.com/bazelbuild/bazel/issues/8061

How to use it:
This flag was introduced to enable TLS for gRPC communication, 'grpc://' or 'grpcs://' are not something default like 'http://' or 'https://'.
At this point we decided that it's time to get rid of this flag and enable TLS by default for all gRPC messages bazel send.

`--remote_cache=someurl.com` would mean use 'someurl.com' to send there gRPC messages via TLS
`--remote_cache=grpc://notls.com` would mean send it to 'notls.com' via gRPC without TLS

Nothing changed for HTTP:
`--remote_cache=http://restcache.com` vs `--remote_cache=https://securecache.come`

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
