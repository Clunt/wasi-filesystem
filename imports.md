<h1><a name="imports">World imports</a></h1>
<ul>
<li>Imports:
<ul>
<li>interface <a href="#wasi_io_error_0_2_0"><code>wasi:io/error@0.2.0</code></a></li>
<li>interface <a href="#wasi_io_poll_0_2_0"><code>wasi:io/poll@0.2.0</code></a></li>
<li>interface <a href="#wasi_io_streams_0_2_0"><code>wasi:io/streams@0.2.0</code></a></li>
<li>interface <a href="#wasi_clocks_wall_clock_0_2_0"><code>wasi:clocks/wall-clock@0.2.0</code></a></li>
<li>interface <a href="#wasi_filesystem_types_0_2_0"><code>wasi:filesystem/types@0.2.0</code></a></li>
<li>interface <a href="#wasi_filesystem_preopens_0_2_0"><code>wasi:filesystem/preopens@0.2.0</code></a></li>
</ul>
</li>
</ul>
<h2><a name="wasi_io_error_0_2_0"></a>Import interface wasi:io/error@0.2.0</h2>
<hr />
<h3>Types</h3>
<h4><a name="error"></a><code>resource error</code></h4>
<p>A resource which represents some error information.</p>
<p>The only method provided by this resource is <code>to-debug-string</code>,
which provides some human-readable information about the error.</p>
<p>In the <code>wasi:io</code> package, this resource is returned through the
<code>wasi:io/streams/stream-error</code> type.</p>
<p>To provide more specific error information, other interfaces may
provide functions to further &quot;downcast&quot; this error into more specific
error information. For example, <a href="#error"><code>error</code></a>s returned in streams derived
from filesystem types to be described using the filesystem's own
error-code type, using the function
<code>wasi:filesystem/types/filesystem-error-code</code>, which takes a parameter
<code>borrow&lt;error&gt;</code> and returns
<code>option&lt;wasi:filesystem/types/error-code&gt;</code>.</p>
<h2>The set of functions which can &quot;downcast&quot; an <a href="#error"><code>error</code></a> into a more
concrete type is open.</h2>
<h3>Functions</h3>
<h4><a name="method_error_to_debug_string"></a><code>[method]error.to-debug-string: func</code></h4>
<p>Returns a string that is suitable to assist humans in debugging
this error.</p>
<p>WARNING: The returned string should not be consumed mechanically!
It may change across platforms, hosts, or other implementation
details. Parsing this string is a major platform-compatibility
hazard.</p>
<h5>Params</h5>
<ul>
<li><a name="method_error_to_debug_string.self"></a><code>self</code>: borrow&lt;<a href="#error"><a href="#error"><code>error</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_error_to_debug_string.0"></a> <code>string</code></li>
</ul>
<h2><a name="wasi_io_poll_0_2_0"></a>Import interface wasi:io/poll@0.2.0</h2>
<p>A poll API intended to let users wait for I/O events on multiple handles
at once.</p>
<hr />
<h3>Types</h3>
<h4><a name="pollable"></a><code>resource pollable</code></h4>
<h2><a href="#pollable"><code>pollable</code></a> represents a single I/O event which may be ready, or not.</h2>
<h3>Functions</h3>
<h4><a name="method_pollable_ready"></a><code>[method]pollable.ready: func</code></h4>
<p>Return the readiness of a pollable. This function never blocks.</p>
<p>Returns <code>true</code> when the pollable is ready, and <code>false</code> otherwise.</p>
<h5>Params</h5>
<ul>
<li><a name="method_pollable_ready.self"></a><code>self</code>: borrow&lt;<a href="#pollable"><a href="#pollable"><code>pollable</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_pollable_ready.0"></a> <code>bool</code></li>
</ul>
<h4><a name="method_pollable_block"></a><code>[method]pollable.block: func</code></h4>
<p><code>block</code> returns immediately if the pollable is ready, and otherwise
blocks until ready.</p>
<p>This function is equivalent to calling <code>poll.poll</code> on a list
containing only this pollable.</p>
<h5>Params</h5>
<ul>
<li><a name="method_pollable_block.self"></a><code>self</code>: borrow&lt;<a href="#pollable"><a href="#pollable"><code>pollable</code></a></a>&gt;</li>
</ul>
<h4><a name="poll"></a><code>poll: func</code></h4>
<p>Poll for completion on a set of pollables.</p>
<p>This function takes a list of pollables, which identify I/O sources of
interest, and waits until one or more of the events is ready for I/O.</p>
<p>The result <code>list&lt;u32&gt;</code> contains one or more indices of handles in the
argument list that is ready for I/O.</p>
<p>If the list contains more elements than can be indexed with a <code>u32</code>
value, this function traps.</p>
<p>A timeout can be implemented by adding a pollable from the
wasi-clocks API to the list.</p>
<p>This function does not return a <code>result</code>; polling in itself does not
do any I/O so it doesn't fail. If any of the I/O sources identified by
the pollables has an error, it is indicated by marking the source as
being reaedy for I/O.</p>
<h5>Params</h5>
<ul>
<li><a name="poll.in"></a><code>in</code>: list&lt;borrow&lt;<a href="#pollable"><a href="#pollable"><code>pollable</code></a></a>&gt;&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="poll.0"></a> list&lt;<code>u32</code>&gt;</li>
</ul>
<h2><a name="wasi_io_streams_0_2_0"></a>Import interface wasi:io/streams@0.2.0</h2>
<p>WASI I/O is an I/O abstraction API which is currently focused on providing
stream types.</p>
<p>In the future, the component model is expected to add built-in stream types;
when it does, they are expected to subsume this API.</p>
<hr />
<h3>Types</h3>
<h4><a name="error"></a><code>type error</code></h4>
<p><a href="#error"><a href="#error"><code>error</code></a></a></p>
<p>
#### <a name="pollable"></a>`type pollable`
[`pollable`](#pollable)
<p>
#### <a name="stream_error"></a>`variant stream-error`
<p>An error for input-stream and output-stream operations.</p>
<h5>Variant Cases</h5>
<ul>
<li>
<p><a name="stream_error.last_operation_failed"></a><code>last-operation-failed</code>: own&lt;<a href="#error"><a href="#error"><code>error</code></a></a>&gt;</p>
<p>The last operation (a write or flush) failed before completion.
<p>More information is available in the <a href="#error"><code>error</code></a> payload.</p>
</li>
<li>
<p><a name="stream_error.closed"></a><code>closed</code></p>
<p>The stream is closed: no more input will be accepted by the
stream. A closed output-stream will return this error on all
future operations.
</li>
</ul>
<h4><a name="input_stream"></a><code>resource input-stream</code></h4>
<p>An input bytestream.</p>
<p><a href="#input_stream"><code>input-stream</code></a>s are <em>non-blocking</em> to the extent practical on underlying
platforms. I/O operations always return promptly; if fewer bytes are
promptly available than requested, they return the number of bytes promptly
available, which could even be zero. To wait for data to be available,
use the <code>subscribe</code> function to obtain a <a href="#pollable"><code>pollable</code></a> which can be polled
for using <code>wasi:io/poll</code>.</p>
<h4><a name="output_stream"></a><code>resource output-stream</code></h4>
<p>An output bytestream.</p>
<h2><a href="#output_stream"><code>output-stream</code></a>s are <em>non-blocking</em> to the extent practical on
underlying platforms. Except where specified otherwise, I/O operations also
always return promptly, after the number of bytes that can be written
promptly, which could even be zero. To wait for the stream to be ready to
accept data, the <code>subscribe</code> function to obtain a <a href="#pollable"><code>pollable</code></a> which can be
polled for using <code>wasi:io/poll</code>.</h2>
<h3>Functions</h3>
<h4><a name="method_input_stream_read"></a><code>[method]input-stream.read: func</code></h4>
<p>Perform a non-blocking read from the stream.</p>
<p>When the source of a <code>read</code> is binary data, the bytes from the source
are returned verbatim. When the source of a <code>read</code> is known to the
implementation to be text, bytes containing the UTF-8 encoding of the
text are returned.</p>
<p>This function returns a list of bytes containing the read data,
when successful. The returned list will contain up to <code>len</code> bytes;
it may return fewer than requested, but not more. The list is
empty when no bytes are available for reading at this time. The
pollable given by <code>subscribe</code> will be ready when more bytes are
available.</p>
<p>This function fails with a <a href="#stream_error"><code>stream-error</code></a> when the operation
encounters an error, giving <code>last-operation-failed</code>, or when the
stream is closed, giving <code>closed</code>.</p>
<p>When the caller gives a <code>len</code> of 0, it represents a request to
read 0 bytes. If the stream is still open, this call should
succeed and return an empty list, or otherwise fail with <code>closed</code>.</p>
<p>The <code>len</code> parameter is a <code>u64</code>, which could represent a list of u8 which
is not possible to allocate in wasm32, or not desirable to allocate as
as a return value by the callee. The callee may return a list of bytes
less than <code>len</code> in size while more bytes are available for reading.</p>
<h5>Params</h5>
<ul>
<li><a name="method_input_stream_read.self"></a><code>self</code>: borrow&lt;<a href="#input_stream"><a href="#input_stream"><code>input-stream</code></a></a>&gt;</li>
<li><a name="method_input_stream_read.len"></a><code>len</code>: <code>u64</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_input_stream_read.0"></a> result&lt;list&lt;<code>u8</code>&gt;, <a href="#stream_error"><a href="#stream_error"><code>stream-error</code></a></a>&gt;</li>
</ul>
<h4><a name="method_input_stream_blocking_read"></a><code>[method]input-stream.blocking-read: func</code></h4>
<p>Read bytes from a stream, after blocking until at least one byte can
be read. Except for blocking, behavior is identical to <code>read</code>.</p>
<h5>Params</h5>
<ul>
<li><a name="method_input_stream_blocking_read.self"></a><code>self</code>: borrow&lt;<a href="#input_stream"><a href="#input_stream"><code>input-stream</code></a></a>&gt;</li>
<li><a name="method_input_stream_blocking_read.len"></a><code>len</code>: <code>u64</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_input_stream_blocking_read.0"></a> result&lt;list&lt;<code>u8</code>&gt;, <a href="#stream_error"><a href="#stream_error"><code>stream-error</code></a></a>&gt;</li>
</ul>
<h4><a name="method_input_stream_skip"></a><code>[method]input-stream.skip: func</code></h4>
<p>Skip bytes from a stream. Returns number of bytes skipped.</p>
<p>Behaves identical to <code>read</code>, except instead of returning a list
of bytes, returns the number of bytes consumed from the stream.</p>
<h5>Params</h5>
<ul>
<li><a name="method_input_stream_skip.self"></a><code>self</code>: borrow&lt;<a href="#input_stream"><a href="#input_stream"><code>input-stream</code></a></a>&gt;</li>
<li><a name="method_input_stream_skip.len"></a><code>len</code>: <code>u64</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_input_stream_skip.0"></a> result&lt;<code>u64</code>, <a href="#stream_error"><a href="#stream_error"><code>stream-error</code></a></a>&gt;</li>
</ul>
<h4><a name="method_input_stream_blocking_skip"></a><code>[method]input-stream.blocking-skip: func</code></h4>
<p>Skip bytes from a stream, after blocking until at least one byte
can be skipped. Except for blocking behavior, identical to <code>skip</code>.</p>
<h5>Params</h5>
<ul>
<li><a name="method_input_stream_blocking_skip.self"></a><code>self</code>: borrow&lt;<a href="#input_stream"><a href="#input_stream"><code>input-stream</code></a></a>&gt;</li>
<li><a name="method_input_stream_blocking_skip.len"></a><code>len</code>: <code>u64</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_input_stream_blocking_skip.0"></a> result&lt;<code>u64</code>, <a href="#stream_error"><a href="#stream_error"><code>stream-error</code></a></a>&gt;</li>
</ul>
<h4><a name="method_input_stream_subscribe"></a><code>[method]input-stream.subscribe: func</code></h4>
<p>Create a <a href="#pollable"><code>pollable</code></a> which will resolve once either the specified stream
has bytes available to read or the other end of the stream has been
closed.
The created <a href="#pollable"><code>pollable</code></a> is a child resource of the <a href="#input_stream"><code>input-stream</code></a>.
Implementations may trap if the <a href="#input_stream"><code>input-stream</code></a> is dropped before
all derived <a href="#pollable"><code>pollable</code></a>s created with this function are dropped.</p>
<h5>Params</h5>
<ul>
<li><a name="method_input_stream_subscribe.self"></a><code>self</code>: borrow&lt;<a href="#input_stream"><a href="#input_stream"><code>input-stream</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_input_stream_subscribe.0"></a> own&lt;<a href="#pollable"><a href="#pollable"><code>pollable</code></a></a>&gt;</li>
</ul>
<h4><a name="method_output_stream_check_write"></a><code>[method]output-stream.check-write: func</code></h4>
<p>Check readiness for writing. This function never blocks.</p>
<p>Returns the number of bytes permitted for the next call to <code>write</code>,
or an error. Calling <code>write</code> with more bytes than this function has
permitted will trap.</p>
<p>When this function returns 0 bytes, the <code>subscribe</code> pollable will
become ready when this function will report at least 1 byte, or an
error.</p>
<h5>Params</h5>
<ul>
<li><a name="method_output_stream_check_write.self"></a><code>self</code>: borrow&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_output_stream_check_write.0"></a> result&lt;<code>u64</code>, <a href="#stream_error"><a href="#stream_error"><code>stream-error</code></a></a>&gt;</li>
</ul>
<h4><a name="method_output_stream_write"></a><code>[method]output-stream.write: func</code></h4>
<p>Perform a write. This function never blocks.</p>
<p>When the destination of a <code>write</code> is binary data, the bytes from
<code>contents</code> are written verbatim. When the destination of a <code>write</code> is
known to the implementation to be text, the bytes of <code>contents</code> are
transcoded from UTF-8 into the encoding of the destination and then
written.</p>
<p>Precondition: check-write gave permit of Ok(n) and contents has a
length of less than or equal to n. Otherwise, this function will trap.</p>
<p>returns Err(closed) without writing if the stream has closed since
the last call to check-write provided a permit.</p>
<h5>Params</h5>
<ul>
<li><a name="method_output_stream_write.self"></a><code>self</code>: borrow&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
<li><a name="method_output_stream_write.contents"></a><code>contents</code>: list&lt;<code>u8</code>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_output_stream_write.0"></a> result&lt;_, <a href="#stream_error"><a href="#stream_error"><code>stream-error</code></a></a>&gt;</li>
</ul>
<h4><a name="method_output_stream_blocking_write_and_flush"></a><code>[method]output-stream.blocking-write-and-flush: func</code></h4>
<p>Perform a write of up to 4096 bytes, and then flush the stream. Block
until all of these operations are complete, or an error occurs.</p>
<p>This is a convenience wrapper around the use of <code>check-write</code>,
<code>subscribe</code>, <code>write</code>, and <code>flush</code>, and is implemented with the
following pseudo-code:</p>
<pre><code class="language-text">let pollable = this.subscribe();
while !contents.is_empty() {
  // Wait for the stream to become writable
  pollable.block();
  let Ok(n) = this.check-write(); // eliding error handling
  let len = min(n, contents.len());
  let (chunk, rest) = contents.split_at(len);
  this.write(chunk  );            // eliding error handling
  contents = rest;
}
this.flush();
// Wait for completion of `flush`
pollable.block();
// Check for any errors that arose during `flush`
let _ = this.check-write();         // eliding error handling
</code></pre>
<h5>Params</h5>
<ul>
<li><a name="method_output_stream_blocking_write_and_flush.self"></a><code>self</code>: borrow&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
<li><a name="method_output_stream_blocking_write_and_flush.contents"></a><code>contents</code>: list&lt;<code>u8</code>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_output_stream_blocking_write_and_flush.0"></a> result&lt;_, <a href="#stream_error"><a href="#stream_error"><code>stream-error</code></a></a>&gt;</li>
</ul>
<h4><a name="method_output_stream_flush"></a><code>[method]output-stream.flush: func</code></h4>
<p>Request to flush buffered output. This function never blocks.</p>
<p>This tells the output-stream that the caller intends any buffered
output to be flushed. the output which is expected to be flushed
is all that has been passed to <code>write</code> prior to this call.</p>
<p>Upon calling this function, the <a href="#output_stream"><code>output-stream</code></a> will not accept any
writes (<code>check-write</code> will return <code>ok(0)</code>) until the flush has
completed. The <code>subscribe</code> pollable will become ready when the
flush has completed and the stream can accept more writes.</p>
<h5>Params</h5>
<ul>
<li><a name="method_output_stream_flush.self"></a><code>self</code>: borrow&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_output_stream_flush.0"></a> result&lt;_, <a href="#stream_error"><a href="#stream_error"><code>stream-error</code></a></a>&gt;</li>
</ul>
<h4><a name="method_output_stream_blocking_flush"></a><code>[method]output-stream.blocking-flush: func</code></h4>
<p>Request to flush buffered output, and block until flush completes
and stream is ready for writing again.</p>
<h5>Params</h5>
<ul>
<li><a name="method_output_stream_blocking_flush.self"></a><code>self</code>: borrow&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_output_stream_blocking_flush.0"></a> result&lt;_, <a href="#stream_error"><a href="#stream_error"><code>stream-error</code></a></a>&gt;</li>
</ul>
<h4><a name="method_output_stream_subscribe"></a><code>[method]output-stream.subscribe: func</code></h4>
<p>Create a <a href="#pollable"><code>pollable</code></a> which will resolve once the output-stream
is ready for more writing, or an error has occured. When this
pollable is ready, <code>check-write</code> will return <code>ok(n)</code> with n&gt;0, or an
error.</p>
<p>If the stream is closed, this pollable is always ready immediately.</p>
<p>The created <a href="#pollable"><code>pollable</code></a> is a child resource of the <a href="#output_stream"><code>output-stream</code></a>.
Implementations may trap if the <a href="#output_stream"><code>output-stream</code></a> is dropped before
all derived <a href="#pollable"><code>pollable</code></a>s created with this function are dropped.</p>
<h5>Params</h5>
<ul>
<li><a name="method_output_stream_subscribe.self"></a><code>self</code>: borrow&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_output_stream_subscribe.0"></a> own&lt;<a href="#pollable"><a href="#pollable"><code>pollable</code></a></a>&gt;</li>
</ul>
<h4><a name="method_output_stream_write_zeroes"></a><code>[method]output-stream.write-zeroes: func</code></h4>
<p>Write zeroes to a stream.</p>
<p>This should be used precisely like <code>write</code> with the exact same
preconditions (must use check-write first), but instead of
passing a list of bytes, you simply pass the number of zero-bytes
that should be written.</p>
<h5>Params</h5>
<ul>
<li><a name="method_output_stream_write_zeroes.self"></a><code>self</code>: borrow&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
<li><a name="method_output_stream_write_zeroes.len"></a><code>len</code>: <code>u64</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_output_stream_write_zeroes.0"></a> result&lt;_, <a href="#stream_error"><a href="#stream_error"><code>stream-error</code></a></a>&gt;</li>
</ul>
<h4><a name="method_output_stream_blocking_write_zeroes_and_flush"></a><code>[method]output-stream.blocking-write-zeroes-and-flush: func</code></h4>
<p>Perform a write of up to 4096 zeroes, and then flush the stream.
Block until all of these operations are complete, or an error
occurs.</p>
<p>This is a convenience wrapper around the use of <code>check-write</code>,
<code>subscribe</code>, <code>write-zeroes</code>, and <code>flush</code>, and is implemented with
the following pseudo-code:</p>
<pre><code class="language-text">let pollable = this.subscribe();
while num_zeroes != 0 {
  // Wait for the stream to become writable
  pollable.block();
  let Ok(n) = this.check-write(); // eliding error handling
  let len = min(n, num_zeroes);
  this.write-zeroes(len);         // eliding error handling
  num_zeroes -= len;
}
this.flush();
// Wait for completion of `flush`
pollable.block();
// Check for any errors that arose during `flush`
let _ = this.check-write();         // eliding error handling
</code></pre>
<h5>Params</h5>
<ul>
<li><a name="method_output_stream_blocking_write_zeroes_and_flush.self"></a><code>self</code>: borrow&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
<li><a name="method_output_stream_blocking_write_zeroes_and_flush.len"></a><code>len</code>: <code>u64</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_output_stream_blocking_write_zeroes_and_flush.0"></a> result&lt;_, <a href="#stream_error"><a href="#stream_error"><code>stream-error</code></a></a>&gt;</li>
</ul>
<h4><a name="method_output_stream_splice"></a><code>[method]output-stream.splice: func</code></h4>
<p>Read from one stream and write to another.</p>
<p>The behavior of splice is equivelant to:</p>
<ol>
<li>calling <code>check-write</code> on the <a href="#output_stream"><code>output-stream</code></a></li>
<li>calling <code>read</code> on the <a href="#input_stream"><code>input-stream</code></a> with the smaller of the
<code>check-write</code> permitted length and the <code>len</code> provided to <code>splice</code></li>
<li>calling <code>write</code> on the <a href="#output_stream"><code>output-stream</code></a> with that read data.</li>
</ol>
<p>Any error reported by the call to <code>check-write</code>, <code>read</code>, or
<code>write</code> ends the splice and reports that error.</p>
<p>This function returns the number of bytes transferred; it may be less
than <code>len</code>.</p>
<h5>Params</h5>
<ul>
<li><a name="method_output_stream_splice.self"></a><code>self</code>: borrow&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
<li><a name="method_output_stream_splice.src"></a><code>src</code>: borrow&lt;<a href="#input_stream"><a href="#input_stream"><code>input-stream</code></a></a>&gt;</li>
<li><a name="method_output_stream_splice.len"></a><code>len</code>: <code>u64</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_output_stream_splice.0"></a> result&lt;<code>u64</code>, <a href="#stream_error"><a href="#stream_error"><code>stream-error</code></a></a>&gt;</li>
</ul>
<h4><a name="method_output_stream_blocking_splice"></a><code>[method]output-stream.blocking-splice: func</code></h4>
<p>Read from one stream and write to another, with blocking.</p>
<p>This is similar to <code>splice</code>, except that it blocks until the
<a href="#output_stream"><code>output-stream</code></a> is ready for writing, and the <a href="#input_stream"><code>input-stream</code></a>
is ready for reading, before performing the <code>splice</code>.</p>
<h5>Params</h5>
<ul>
<li><a name="method_output_stream_blocking_splice.self"></a><code>self</code>: borrow&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;</li>
<li><a name="method_output_stream_blocking_splice.src"></a><code>src</code>: borrow&lt;<a href="#input_stream"><a href="#input_stream"><code>input-stream</code></a></a>&gt;</li>
<li><a name="method_output_stream_blocking_splice.len"></a><code>len</code>: <code>u64</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_output_stream_blocking_splice.0"></a> result&lt;<code>u64</code>, <a href="#stream_error"><a href="#stream_error"><code>stream-error</code></a></a>&gt;</li>
</ul>
<h2><a name="wasi_clocks_wall_clock_0_2_0"></a>Import interface wasi:clocks/wall-clock@0.2.0</h2>
<p>WASI Wall Clock is a clock API intended to let users query the current
time. The name &quot;wall&quot; makes an analogy to a &quot;clock on the wall&quot;, which
is not necessarily monotonic as it may be reset.</p>
<p>It is intended to be portable at least between Unix-family platforms and
Windows.</p>
<p>A wall clock is a clock which measures the date and time according to
some external reference.</p>
<p>External references may be reset, so this clock is not necessarily
monotonic, making it unsuitable for measuring elapsed time.</p>
<p>It is intended for reporting the current date and time for humans.</p>
<hr />
<h3>Types</h3>
<h4><a name="datetime"></a><code>record datetime</code></h4>
<p>A time and date in seconds plus nanoseconds.</p>
<h5>Record Fields</h5>
<ul>
<li><a name="datetime.seconds"></a><code>seconds</code>: <code>u64</code></li>
<li><a name="datetime.nanoseconds"></a><code>nanoseconds</code>: <code>u32</code></li>
</ul>
<hr />
<h3>Functions</h3>
<h4><a name="now"></a><code>now: func</code></h4>
<p>Read the current value of the clock.</p>
<p>This clock is not monotonic, therefore calling this function repeatedly
will not necessarily produce a sequence of non-decreasing values.</p>
<p>The returned timestamps represent the number of seconds since
1970-01-01T00:00:00Z, also known as <a href="https://pubs.opengroup.org/onlinepubs/9699919799/xrat/V4_xbd_chap04.html#tag_21_04_16">POSIX's Seconds Since the Epoch</a>,
also known as <a href="https://en.wikipedia.org/wiki/Unix_time">Unix Time</a>.</p>
<p>The nanoseconds field of the output is always less than 1000000000.</p>
<h5>Return values</h5>
<ul>
<li><a name="now.0"></a> <a href="#datetime"><a href="#datetime"><code>datetime</code></a></a></li>
</ul>
<h4><a name="resolution"></a><code>resolution: func</code></h4>
<p>Query the resolution of the clock.</p>
<p>The nanoseconds field of the output is always less than 1000000000.</p>
<h5>Return values</h5>
<ul>
<li><a name="resolution.0"></a> <a href="#datetime"><a href="#datetime"><code>datetime</code></a></a></li>
</ul>
<h2><a name="wasi_filesystem_types_0_2_0"></a>Import interface wasi:filesystem/types@0.2.0</h2>
<p>WASI filesystem主要是文件系统API，
旨在让用户在其现有文件系统上，运行访问其文件的WASI程序，
而不会产生大量的额外开销。</p>
<p>它旨在在Unix系列平台和Windows之间大致可移植，尽管它并未隐藏许多主要差异。</p>
<p>路径作为接口类型<code>string</code>传递，
意味着它们必须由一系列Unicode标量值（Unicode Scalar Values, USVs）组成。
有些文件系统可能包含此API无法访问的路径。</p>
<p>WASI中的目录分隔符总是正斜杠（<code>/</code>）。</p>
<p>WASI中的所有路径都是相对路径，并且相对于引用基目录的<a href="#descriptor"><code>descriptor</code></a>进行解释。
如果WASI函数的<code>path</code>参数以<code>/</code>开头，或者解析<code>path</code>的任意步骤中，
包括<code>..</code>和符号连接阶段到达基目录外的目录，或到达底层文件系统中绝对路径或根路径的符号连接，
该函数将以<a href="#error_code.not_permitted"><code>error-code::not-permitted</code></a>失败。</p>
<p>关于WASI路径解析和沙盒化的更多信息，请参阅<a href="https://github.com/Clunt/wasi-filesystem/blob/main/path-resolution.md">WASI文件系统路径解析</a>.</p>
<hr />
<h3>Types</h3>
<h4><a name="input_stream"></a><code>type input-stream</code></h4>
<p><a href="#input_stream"><a href="#input_stream"><code>input-stream</code></a></a></p>
<p>
#### <a name="output_stream"></a>`type output-stream`
[`output-stream`](#output_stream)
<p>
#### <a name="error"></a>`type error`
[`error`](#error)
<p>
#### <a name="datetime"></a>`type datetime`
[`datetime`](#datetime)
<p>
#### <a name="filesize"></a>`type filesize`
`u64`
<p>文件大小或文件内区域的长度。
<h4><a name="descriptor_type"></a><code>enum descriptor-type</code></h4>
<p>descriptor(描述符)引用的文件系统对象的类型。</p>
<p>注意：在WASI的早期版本中，这被称为<code>filetype</code>。</p>
<h5>Enum Cases</h5>
<ul>
<li>
<p><a name="descriptor_type.unknown"></a><code>unknown</code></p>
<p>descriptor(描述符)或file(文件)的类型未知或与指定的任何其他类型不同。
</li>
<li>
<p><a name="descriptor_type.block_device"></a><code>block-device</code></p>
<p>描述符引用一个块设备索引节点(a block device inode)
</li>
<li>
<p><a name="descriptor_type.character_device"></a><code>character-device</code></p>
<p>描述符引用一个字节设备索引节点(a character device inode)。
</li>
<li>
<p><a name="descriptor_type.directory"></a><code>directory</code></p>
<p>描述符引用一个目录索引节点(a directory inode)。
</li>
<li>
<p><a name="descriptor_type.fifo"></a><code>fifo</code></p>
<p>描述符引用一个命名管道(a named pipe)。
</li>
<li>
<p><a name="descriptor_type.symbolic_link"></a><code>symbolic-link</code></p>
<p>描述符引用一个符号连接索引节点(a symbolic link inode)。
</li>
<li>
<p><a name="descriptor_type.regular_file"></a><code>regular-file</code></p>
<p>描述符引用一个常规文件索引节点(a regular file inode)。
</li>
<li>
<p><a name="descriptor_type.socket"></a><code>socket</code></p>
<p>描述符引用一个套接字(a socket)。
</li>
</ul>
<h4><a name="descriptor_flags"></a><code>flags descriptor-flags</code></h4>
<p>描述符标志。</p>
<p>注意：在WASI的早期版本中，这被称为<code>fdflags</code>。</p>
<h5>Flags members</h5>
<ul>
<li>
<p><a name="descriptor_flags.read"></a><code>read</code>: </p>
<p>读模式：数据可读。
</li>
<li>
<p><a name="descriptor_flags.write"></a><code>write</code>: </p>
<p>写模式：数据可写。
</li>
<li>
<p><a name="descriptor_flags.mutate_directory"></a><code>mutate-directory</code>: </p>
<p>请求(request)根据同步I/O文件完整性完成来执行写操作。
存储在文件和文件元数据(metadata)中的数据会被同步。
这类似于POSIX中`O_SYNC`。
<p>WASI尚未定义此操作的精确语义。目前，它应被解析为请求(request)，而不是要求(requirement)。
file-integrity-sync，请求(request)根据同步I/O数据完整性完成来执行写操作。
只有存储在文件中的数据被同步，这类似于POSIX中<code>O_DSYNC</code>。</p>
<p>WASI尚未定义此操作的精确语义。目前，它应被解析为请求(request)，而不是要求(requirement)。
data-integrity-sync，请求(request)以写操作同级别的完整性来执行读操作。
这类似于POSIX中<code>O_RSYNC</code>。</p>
<p>WASI尚未定义此操作的精确语义。目前，它应被解析为请求(request)，而不是要求(requirement)。
requested-write-sync，更改目录模式(Mutating directories mode)：目录内容可能会发生变化。</p>
<p>当这个标志在描述符上未设置时，
使用描述符的操作将创建、重命名、删除、修改文件系统对象的数据或元数据，或获取允许这些操作的另一个句柄，
如果它们本来会成功的话，则将以<a href="#error_code.read_only"><code>error-code::read-only</code></a>失败。</p>
<p>这只能在目录上设置。</p>
</li>
</ul>
<h4><a name="path_flags"></a><code>flags path-flags</code></h4>
<p>确定路径解析方法的标志。</p>
<h5>Flags members</h5>
<ul>
<li><a name="path_flags.symlink_follow"></a><code>symlink-follow</code>: <p>只要解析的路径对应符号链接，它就被展开。
</li>
</ul>
<h4><a name="open_flags"></a><code>flags open-flags</code></h4>
<p>用于<code>open-at</code>的打开标志。</p>
<h5>Flags members</h5>
<ul>
<li>
<p><a name="open_flags.create"></a><code>create</code>: </p>
<p>如果文件不存在则创建，类似于POSIX中的`O_CREAT`。
</li>
<li>
<p><a name="open_flags.directory"></a><code>directory</code>: </p>
<p>如果不是目录则失败，类似于POSIX中的`O_DIRECTORY`。
</li>
<li>
<p><a name="open_flags.exclusive"></a><code>exclusive</code>: </p>
<p>如果文件已存在则失败，类似于POSIX中的`O_EXCL`。
</li>
<li>
<p><a name="open_flags.truncate"></a><code>truncate</code>: </p>
<p>文件尺寸截断为0，类似于POSIX中的`O_TRUNC`。
</li>
</ul>
<h4><a name="link_count"></a><code>type link-count</code></h4>
<p><code>u64</code></p>
<p>指向索引节点(inode)的硬链接(hard links)数量。
<h4><a name="descriptor_stat"></a><code>record descriptor-stat</code></h4>
<p>文件属性(File attributes)。</p>
<p>注意：在WASI的早期版本中，这被称为<code>filestat</code>。</p>
<h5>Record Fields</h5>
<ul>
<li>
<p><a name="descriptor_stat.type"></a><code>type</code>: <a href="#descriptor_type"><a href="#descriptor_type"><code>descriptor-type</code></a></a></p>
<p>文件类型(File type)。
</li>
<li>
<p><a name="descriptor_stat.link_count"></a><a href="#link_count"><code>link-count</code></a>: <a href="#link_count"><a href="#link_count"><code>link-count</code></a></a></p>
<p>文件硬链接数量(Number of hard links to the file)。
</li>
<li>
<p><a name="descriptor_stat.size"></a><code>size</code>: <a href="#filesize"><a href="#filesize"><code>filesize</code></a></a></p>
<p>对于普通文件，指的是以字节为单位的文件大小。
对于符号链接，指的是符号链接中包含的路径名(pathname)的字节长度。
</li>
<li>
<p><a name="descriptor_stat.data_access_timestamp"></a><code>data-access-timestamp</code>: option&lt;<a href="#datetime"><a href="#datetime"><code>datetime</code></a></a>&gt;</p>
<p>最后一次数据访问时间戳。
<p>如果<code>option</code>为none，则平台不维护该文件的访问时间戳。</p>
</li>
<li>
<p><a name="descriptor_stat.data_modification_timestamp"></a><code>data-modification-timestamp</code>: option&lt;<a href="#datetime"><a href="#datetime"><code>datetime</code></a></a>&gt;</p>
<p>最后一次数据修改时间戳。
<p>如果<code>option</code>为none，则平台不维护该文件的修改时间戳。</p>
</li>
<li>
<p><a name="descriptor_stat.status_change_timestamp"></a><code>status-change-timestamp</code>: option&lt;<a href="#datetime"><a href="#datetime"><code>datetime</code></a></a>&gt;</p>
<p>最后一次文件状态更改时间戳。
<p>如果<code>option</code>为none，则平台不维护该文件的状态更改时间戳。</p>
</li>
</ul>
<h4><a name="new_timestamp"></a><code>variant new-timestamp</code></h4>
<p>当设置时间戳时，它表示要设置的值。</p>
<h5>Variant Cases</h5>
<ul>
<li>
<p><a name="new_timestamp.no_change"></a><code>no-change</code></p>
<p>将时间戳设置为之前的值。
</li>
<li>
<p><a name="new_timestamp.now"></a><a href="#now"><code>now</code></a></p>
<p>将时间戳设置为与文件系统关联的系统时钟的当前时间。
</li>
<li>
<p><a name="new_timestamp.timestamp"></a><code>timestamp</code>: <a href="#datetime"><a href="#datetime"><code>datetime</code></a></a></p>
<p>将时间戳设置为给定值。
</li>
</ul>
<h4><a name="directory_entry"></a><code>record directory-entry</code></h4>
<p>目录项。</p>
<h5>Record Fields</h5>
<ul>
<li>
<p><a name="directory_entry.type"></a><code>type</code>: <a href="#descriptor_type"><a href="#descriptor_type"><code>descriptor-type</code></a></a></p>
<p>该目录项引用的文件类型。
</li>
<li>
<p><a name="directory_entry.name"></a><code>name</code>: <code>string</code></p>
<p>对象名称。
</li>
</ul>
<h4><a name="error_code"></a><code>enum error-code</code></h4>
<p>函数返回的错误码，类似于POSIX中的<code>errno</code>。
并非所有这些错误码都由此API提供的函数返回；
其中一些用于更高级库层，而其他一些仅用于与POSIX对齐。</p>
<h5>Enum Cases</h5>
<ul>
<li>
<p><a name="error_code.access"></a><code>access</code></p>
<p>权限被拒绝，类似于POSIX中的`EACCES`。
</li>
<li>
<p><a name="error_code.would_block"></a><code>would-block</code></p>
<p>资源不可用，或操作将阻塞，类似于POSIX中的`EAGAIN`和`EWOULDBLOCK`。
</li>
<li>
<p><a name="error_code.already"></a><code>already</code></p>
<p>连接已在进行中，类似于POSIX中的`EALREADY`。
</li>
<li>
<p><a name="error_code.bad_descriptor"></a><code>bad-descriptor</code></p>
<p>错误的描述符，类似于POSIX中的`EBADF`。
</li>
<li>
<p><a name="error_code.busy"></a><code>busy</code></p>
<p>设备或资源繁忙，类似于POSIX中的`EBUSY`。
</li>
<li>
<p><a name="error_code.deadlock"></a><code>deadlock</code></p>
<p>资源死锁将发生，类似于POSIX中的`EDEADLK`。
</li>
<li>
<p><a name="error_code.quota"></a><code>quota</code></p>
<p>超出存储配额，类似于POSIX中的`EDQUOT`。
</li>
<li>
<p><a name="error_code.exist"></a><code>exist</code></p>
<p>文件存在，类似于POSIX中的`EEXIST`。
</li>
<li>
<p><a name="error_code.file_too_large"></a><code>file-too-large</code></p>
<p>文件过大，类似于POSIX中的`EFBIG`。
</li>
<li>
<p><a name="error_code.illegal_byte_sequence"></a><code>illegal-byte-sequence</code></p>
<p>非法字符序列，类似于POSIX中的`EILSEQ`。
</li>
<li>
<p><a name="error_code.in_progress"></a><code>in-progress</code></p>
<p>进行中的操作，类似于POSIX中的`EINPROGRESS`。
</li>
<li>
<p><a name="error_code.interrupted"></a><code>interrupted</code></p>
<p>中断函数，类似于POSIX中的`EINTR`。
</li>
<li>
<p><a name="error_code.invalid"></a><code>invalid</code></p>
<p>无效参数，类似于POSIX中的`EINVAL`。
</li>
<li>
<p><a name="error_code.io"></a><code>io</code></p>
<p>I/O错误，类似于POSIX中的`EIO`。
</li>
<li>
<p><a name="error_code.is_directory"></a><code>is-directory</code></p>
<p>是一个目录，类似于POSIX中的`EISDIR`。
</li>
<li>
<p><a name="error_code.loop"></a><code>loop</code></p>
<p>符号链接层级过多，类似于POSIX中的`ELOOP`。
</li>
<li>
<p><a name="error_code.too_many_links"></a><code>too-many-links</code></p>
<p>链接过多，类似于POSIX中的`EMLINK`。
</li>
<li>
<p><a name="error_code.message_size"></a><code>message-size</code></p>
<p>消息过大，类似于POSIX中的`EMSGSIZE`。
</li>
<li>
<p><a name="error_code.name_too_long"></a><code>name-too-long</code></p>
<p>文件名过长，类似于POSIX中的`ENAMETOOLONG`。
</li>
<li>
<p><a name="error_code.no_device"></a><code>no-device</code></p>
<p>无此设备，类似于POSIX中的`ENODEV`。
</li>
<li>
<p><a name="error_code.no_entry"></a><code>no-entry</code></p>
<p>无此文件或目录，类似于POSIX中的`ENOENT`。
</li>
<li>
<p><a name="error_code.no_lock"></a><code>no-lock</code></p>
<p>无可用锁，类似于POSIX中的`ENOLCK`。
</li>
<li>
<p><a name="error_code.insufficient_memory"></a><code>insufficient-memory</code></p>
<p>空间不足，类似于POSIX中的`ENOMEM`。
</li>
<li>
<p><a name="error_code.insufficient_space"></a><code>insufficient-space</code></p>
<p>设备上无剩余空间，类似于POSIX中的`ENOSPC`。
</li>
<li>
<p><a name="error_code.not_directory"></a><code>not-directory</code></p>
<p>不是目录或指向目录的符号链接，类似于POSIX中的`ENOTDIR`。
</li>
<li>
<p><a name="error_code.not_empty"></a><code>not-empty</code></p>
<p>目录非空，类似于POSIX中的`ENOTEMPTY`。
</li>
<li>
<p><a name="error_code.not_recoverable"></a><code>not-recoverable</code></p>
<p>状态无法恢复，类似于POSIX中的`ENOTRECOVERABLE`。
</li>
<li>
<p><a name="error_code.unsupported"></a><code>unsupported</code></p>
<p>不支持，类似于POSIX中的`ENOTSUP`和`ENOSYS`。
</li>
<li>
<p><a name="error_code.no_tty"></a><code>no-tty</code></p>
<p>不适当的I/O控制操作，类似于POSIX中的`ENOTTY`。
</li>
<li>
<p><a name="error_code.no_such_device"></a><code>no-such-device</code></p>
<p>无此设备或地址，类似于POSIX中的`ENXIO`。
</li>
<li>
<p><a name="error_code.overflow"></a><code>overflow</code></p>
<p>值过大无法存储在数据类型中，类似于POSIX中的`EOVERFLOW`。
</li>
<li>
<p><a name="error_code.not_permitted"></a><code>not-permitted</code></p>
<p>操作不允许，类似于POSIX中的`EPERM`。
</li>
<li>
<p><a name="error_code.pipe"></a><code>pipe</code></p>
<p>管道中断，类似于POSIX中的`EPIPE`。
</li>
<li>
<p><a name="error_code.read_only"></a><code>read-only</code></p>
<p>只读文件系统，类似于POSIX中的`EROFS`。
</li>
<li>
<p><a name="error_code.invalid_seek"></a><code>invalid-seek</code></p>
<p>无效查找，类似于POSIX中的`ESPIPE`。
</li>
<li>
<p><a name="error_code.text_file_busy"></a><code>text-file-busy</code></p>
<p>文本文件繁忙，类似于POSIX中的`ETXTBSY`。
</li>
<li>
<p><a name="error_code.cross_device"></a><code>cross-device</code></p>
<p>跨设备链接，类似于POSIX中的`EXDEV`。
</li>
</ul>
<h4><a name="advice"></a><code>enum advice</code></h4>
<p>文件或内存访问模式的建议信息。</p>
<h5>Enum Cases</h5>
<ul>
<li>
<p><a name="advice.normal"></a><code>normal</code></p>
<p>应用程序对其与指定数据相关的行为没有建议。
</li>
<li>
<p><a name="advice.sequential"></a><code>sequential</code></p>
<p>应用程序期望以低偏移量到高偏移量顺序访问指定的数据。
</li>
<li>
<p><a name="advice.random"></a><code>random</code></p>
<p>应用程序期望以随机顺序访问指定的数据。
</li>
<li>
<p><a name="advice.will_need"></a><code>will-need</code></p>
<p>应用程序期望在不久的将来访问指定的数据。
</li>
<li>
<p><a name="advice.dont_need"></a><code>dont-need</code></p>
<p>应用程序预计在不久的将来不会访问指定的数据。
</li>
<li>
<p><a name="advice.no_reuse"></a><code>no-reuse</code></p>
<p>应用程序期望访问指定的数据一次，然后不再使用它。
</li>
</ul>
<h4><a name="metadata_hash_value"></a><code>record metadata-hash-value</code></h4>
<p>一个128位的哈希值，因为wasm没有128位的整数类型而被分成几个部分。</p>
<h5>Record Fields</h5>
<ul>
<li>
<p><a name="metadata_hash_value.lower"></a><code>lower</code>: <code>u64</code></p>
<p>128位哈希值中的64位。
</li>
<li>
<p><a name="metadata_hash_value.upper"></a><code>upper</code>: <code>u64</code></p>
<p>128位哈希值中的另外64位。
</li>
</ul>
<h4><a name="descriptor"></a><code>resource descriptor</code></h4>
<p>描述符是对文件系统对象的引用，该对象可以是文件、目录、命名管道、特殊文件或可以对其进行文件系统调用的其他对象。</p>
<h4><a name="directory_entry_stream"></a><code>resource directory-entry-stream</code></h4>
<h2>目录项的流。</h2>
<h3>Functions</h3>
<h4><a name="method_descriptor_read_via_stream"></a><code>[method]descriptor.read-via-stream: func</code></h4>
<p>如果可用，返回用于读取文件的流。</p>
<p>可能会以error-code失败，描述为什么无法读取文件。</p>
<p>多个读、写和追加流可能活动于同一个打开的文件，并且它们不会相互干扰。</p>
<p>注意：其允许使用<code>read-stream</code>，类似于POSIX中的<code>read</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_read_via_stream.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
<li><a name="method_descriptor_read_via_stream.offset"></a><code>offset</code>: <a href="#filesize"><a href="#filesize"><code>filesize</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_read_via_stream.0"></a> result&lt;own&lt;<a href="#input_stream"><a href="#input_stream"><code>input-stream</code></a></a>&gt;, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_write_via_stream"></a><code>[method]descriptor.write-via-stream: func</code></h4>
<p>如果可用，返回用于写入文件的流。</p>
<p>可能会以error-code失败，描述为什么无法写入文件。</p>
<p>注意：其允许使用<code>write-stream</code>，类似于POSIX中的<code>write</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_write_via_stream.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
<li><a name="method_descriptor_write_via_stream.offset"></a><code>offset</code>: <a href="#filesize"><a href="#filesize"><code>filesize</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_write_via_stream.0"></a> result&lt;own&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_append_via_stream"></a><code>[method]descriptor.append-via-stream: func</code></h4>
<p>如果可用，返回用于追加文件的流。</p>
<p>可能会以error-code失败，描述为什么无法追加文件。</p>
<p>注意：其允许使用<code>write-stream</code>，类似于POSIX中的<code>write</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_append_via_stream.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_append_via_stream.0"></a> result&lt;own&lt;<a href="#output_stream"><a href="#output_stream"><code>output-stream</code></a></a>&gt;, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_advise"></a><code>[method]descriptor.advise: func</code></h4>
<p>在描述符上提供文件建议信息。</p>
<p>类似于POSIX中的<code>posix_fadvise</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_advise.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
<li><a name="method_descriptor_advise.offset"></a><code>offset</code>: <a href="#filesize"><a href="#filesize"><code>filesize</code></a></a></li>
<li><a name="method_descriptor_advise.length"></a><code>length</code>: <a href="#filesize"><a href="#filesize"><code>filesize</code></a></a></li>
<li><a name="method_descriptor_advise.advice"></a><a href="#advice"><code>advice</code></a>: <a href="#advice"><a href="#advice"><code>advice</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_advise.0"></a> result&lt;_, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_sync_data"></a><code>[method]descriptor.sync-data: func</code></h4>
<p>同步文件数据至磁盘。</p>
<p>如果文件描述符未打开写入，则此函数会成功但没有效果。</p>
<p>注意：其类似于POSIX中的<code>fdatasync</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_sync_data.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_sync_data.0"></a> result&lt;_, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_get_flags"></a><code>[method]descriptor.get-flags: func</code></h4>
<p>获取与描述符关联的标志。</p>
<p>注意：其返回的标志类似于POSIX中的<code>fcntl(fd, F_GETFL)</code>。</p>
<p>注意：该值在WASI的早期版本中是<code>fdstat_get</code>的返回值<code>fs_flags</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_get_flags.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_get_flags.0"></a> result&lt;<a href="#descriptor_flags"><a href="#descriptor_flags"><code>descriptor-flags</code></a></a>, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_get_type"></a><code>[method]descriptor.get-type: func</code></h4>
<p>获取描述符的动态类型。</p>
<p>注意：此返回与<code>stat</code>、<code>stat-at</code>等返回的<code>fd-stat</code>d的 <code>type</code>字段的值相同。</p>
<p>注意：此返回的标志类似于POSIX中<code>fstat</code>提供的<code>st_mode &amp; S_IFMT</code>值。</p>
<p>注意：此返回的值是早期 WASI 版本中<code>fdstat_get</code>返回的<code>fs_filetype</code>值。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_get_type.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_get_type.0"></a> result&lt;<a href="#descriptor_type"><a href="#descriptor_type"><code>descriptor-type</code></a></a>, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_set_size"></a><code>[method]descriptor.set-size: func</code></h4>
<p>调整已打开文件的大小。如果增加文件的大小，额外的字节将被填充为零。</p>
<p>注意：在早期版本的 WASI 中，这被称为 <code>fd_filestat_set_size</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_set_size.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
<li><a name="method_descriptor_set_size.size"></a><code>size</code>: <a href="#filesize"><a href="#filesize"><code>filesize</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_set_size.0"></a> result&lt;_, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_set_times"></a><code>[method]descriptor.set-times: func</code></h4>
<p>调整已打开文件或目录的时间戳。</p>
<p>注意：其类似于POSIX中的<code>futimens</code>。</p>
<p>注意：在早期版本的 WASI 中，这被称为 <code>fd_filestat_set_times</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_set_times.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
<li><a name="method_descriptor_set_times.data_access_timestamp"></a><code>data-access-timestamp</code>: <a href="#new_timestamp"><a href="#new_timestamp"><code>new-timestamp</code></a></a></li>
<li><a name="method_descriptor_set_times.data_modification_timestamp"></a><code>data-modification-timestamp</code>: <a href="#new_timestamp"><a href="#new_timestamp"><code>new-timestamp</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_set_times.0"></a> result&lt;_, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_read"></a><code>[method]descriptor.read: func</code></h4>
<p>在不使用和更新描述符的偏移量的情况下，从描述符中读取数据。</p>
<p>该函数返回一个字节列表，包含已读取的数据，
以及一个布尔值，当布尔值为true，表示已达到文件末尾。
返回的列表将包含最多length字节；
如果达到文件末尾或者 I/O 操作被中断，可能会返回少于请求的字节数。</p>
<p>将来，可能会更改为返回<code>stream&lt;u8, error-code&gt;</code>。</p>
<p>注意：其类似于POSIX中的<code>pread</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_read.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
<li><a name="method_descriptor_read.length"></a><code>length</code>: <a href="#filesize"><a href="#filesize"><code>filesize</code></a></a></li>
<li><a name="method_descriptor_read.offset"></a><code>offset</code>: <a href="#filesize"><a href="#filesize"><code>filesize</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_read.0"></a> result&lt;(list&lt;<code>u8</code>&gt;, <code>bool</code>), <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_write"></a><code>[method]descriptor.write: func</code></h4>
<p>在不使用和更新描述符的偏移量的情况下，向描述符写入数据。</p>
<p>可以向文件末尾写入数据；
文件会被扩展到写入的范围，并且在先前末尾和写入开始之间的字节会被设置为零。</p>
<p>将来，可能会更改为返回<code>stream&lt;u8, error-code&gt;</code>。</p>
<p>注意：其类似于POSIX中的<code>pwrite</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_write.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
<li><a name="method_descriptor_write.buffer"></a><code>buffer</code>: list&lt;<code>u8</code>&gt;</li>
<li><a name="method_descriptor_write.offset"></a><code>offset</code>: <a href="#filesize"><a href="#filesize"><code>filesize</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_write.0"></a> result&lt;<a href="#filesize"><a href="#filesize"><code>filesize</code></a></a>, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_read_directory"></a><code>[method]descriptor.read-directory: func</code></h4>
<p>从目录中读取目录条目。</p>
<p>在文件系统中，如果目录包含指向自身和其父目录的条目，
通常分别命名为<code>.</code>和<code>..</code>，这些条目将被省略。</p>
<p>这总是返回一个新的流，该流从目录的开头开始。同一目录上可以有多个活动的流，它们不会相互干扰。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_read_directory.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_read_directory.0"></a> result&lt;own&lt;<a href="#directory_entry_stream"><a href="#directory_entry_stream"><code>directory-entry-stream</code></a></a>&gt;, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_sync"></a><code>[method]descriptor.sync: func</code></h4>
<p>同步文件的数据和元数据至磁盘。</p>
<p>如果文件描述符未打开写入，则此函数会成功但没有效果。</p>
<p>注意：其类似于POSIX中的<code>fsync</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_sync.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_sync.0"></a> result&lt;_, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_create_directory_at"></a><code>[method]descriptor.create-directory-at: func</code></h4>
<p>创建目录。</p>
<p>注意：其类似于POSIX中的<code>mkdirat</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_create_directory_at.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
<li><a name="method_descriptor_create_directory_at.path"></a><code>path</code>: <code>string</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_create_directory_at.0"></a> result&lt;_, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_stat"></a><code>[method]descriptor.stat: func</code></h4>
<p>返回已打开文件或目录的属性。</p>
<p>注意：其类似于POSIX中的<code>fstat</code>，除了它不返回设备和索引节点的信息。
要测试两个文件描述符是否指向同一底层文件系统对象，使用<code>is-same-object</code>。
要获取其他可以用来确定文件是否已被修改的数据，使用<code>metadata-hash</code>。</p>
<p>注意：在早期的WASI版本中被称为<code>fd_filestat_get</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_stat.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_stat.0"></a> result&lt;<a href="#descriptor_stat"><a href="#descriptor_stat"><code>descriptor-stat</code></a></a>, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_stat_at"></a><code>[method]descriptor.stat-at: func</code></h4>
<p>返回文件或目录的属性。</p>
<p>注意：其类似于POSIX中的<code>fstatat</code>，除了它不返回设备和索引节点的信息。
请参阅<code>stat</code>描述中关于替代方案的讨论。</p>
<p>注意：在早期的WASI版本中被称为<code>path_filestat_get</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_stat_at.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
<li><a name="method_descriptor_stat_at.path_flags"></a><a href="#path_flags"><code>path-flags</code></a>: <a href="#path_flags"><a href="#path_flags"><code>path-flags</code></a></a></li>
<li><a name="method_descriptor_stat_at.path"></a><code>path</code>: <code>string</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_stat_at.0"></a> result&lt;<a href="#descriptor_stat"><a href="#descriptor_stat"><code>descriptor-stat</code></a></a>, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_set_times_at"></a><code>[method]descriptor.set-times-at: func</code></h4>
<p>调整文件或目录的时间戳。</p>
<p>注意：其类似于POSIX中的<code>utimensat</code>。</p>
<p>注意：在早期的WASI版本中被称为<code>path_filestat_set_times</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_set_times_at.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
<li><a name="method_descriptor_set_times_at.path_flags"></a><a href="#path_flags"><code>path-flags</code></a>: <a href="#path_flags"><a href="#path_flags"><code>path-flags</code></a></a></li>
<li><a name="method_descriptor_set_times_at.path"></a><code>path</code>: <code>string</code></li>
<li><a name="method_descriptor_set_times_at.data_access_timestamp"></a><code>data-access-timestamp</code>: <a href="#new_timestamp"><a href="#new_timestamp"><code>new-timestamp</code></a></a></li>
<li><a name="method_descriptor_set_times_at.data_modification_timestamp"></a><code>data-modification-timestamp</code>: <a href="#new_timestamp"><a href="#new_timestamp"><code>new-timestamp</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_set_times_at.0"></a> result&lt;_, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_link_at"></a><code>[method]descriptor.link-at: func</code></h4>
<p>创建硬链接(hard link)。</p>
<p>注意：其类似于POSIX中的<code>linkat</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_link_at.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
<li><a name="method_descriptor_link_at.old_path_flags"></a><code>old-path-flags</code>: <a href="#path_flags"><a href="#path_flags"><code>path-flags</code></a></a></li>
<li><a name="method_descriptor_link_at.old_path"></a><code>old-path</code>: <code>string</code></li>
<li><a name="method_descriptor_link_at.new_descriptor"></a><code>new-descriptor</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
<li><a name="method_descriptor_link_at.new_path"></a><code>new-path</code>: <code>string</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_link_at.0"></a> result&lt;_, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_open_at"></a><code>[method]descriptor.open-at: func</code></h4>
<p>打开文件或目录。</p>
<p>返回的描述符不能保证是当前未打开的最低编号(lowest-numbered)描述符，
它是随机的以防止应用程序依赖于对索引的假设，因为在多线程环境中这样做是容易出错的。
返回的描述符保证小于2**31。</p>
<p>如果<code>flags</code>包含<a href="#descriptor_flags.mutate_directory"><code>descriptor-flags::mutate-directory</code></a>，
并且基础描述符没有设置<a href="#descriptor_flags.mutate_directory"><code>descriptor-flags::mutate-directory</code></a>，
则<code>open-at</code>将失败，并返回<a href="#error_code.read_only"><code>error-code::read-only</code></a>。</p>
<p>如果<code>flags</code>包含<code>write</code>或<code>mutate-directory</code>，或者<a href="#open_flags"><code>open-flags</code></a>包含<code>truncate</code>或<code>create</code>，
并且基础描述符没有设置 <a href="#descriptor_flags.mutate_directory"><code>descriptor-flags::mutate-directory</code></a>，
则 <code>open-at</code>将失败，并返回<a href="#error_code.read_only"><code>error-code::read-only</code></a>。</p>
<p>注意：其类似于POSIX中的<code>openat</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_open_at.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
<li><a name="method_descriptor_open_at.path_flags"></a><a href="#path_flags"><code>path-flags</code></a>: <a href="#path_flags"><a href="#path_flags"><code>path-flags</code></a></a></li>
<li><a name="method_descriptor_open_at.path"></a><code>path</code>: <code>string</code></li>
<li><a name="method_descriptor_open_at.open_flags"></a><a href="#open_flags"><code>open-flags</code></a>: <a href="#open_flags"><a href="#open_flags"><code>open-flags</code></a></a></li>
<li><a name="method_descriptor_open_at.flags"></a><code>flags</code>: <a href="#descriptor_flags"><a href="#descriptor_flags"><code>descriptor-flags</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_open_at.0"></a> result&lt;own&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_readlink_at"></a><code>[method]descriptor.readlink-at: func</code></h4>
<p>读取符号连接的内容。</p>
<p>如果内容包含底层文件系统中的绝对路径或根路径，
此函数将失败，并返回 <a href="#error_code.not_permitted"><code>error-code::not-permitted</code></a>。</p>
<p>注意：其类似于POSIX中的<code>readlinkat</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_readlink_at.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
<li><a name="method_descriptor_readlink_at.path"></a><code>path</code>: <code>string</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_readlink_at.0"></a> result&lt;<code>string</code>, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_remove_directory_at"></a><code>[method]descriptor.remove-directory-at: func</code></h4>
<p>删除目录。</p>
<p>如果目录非空，返回<a href="#error_code.not_empty"><code>error-code::not-empty</code></a>。</p>
<p>注意：其类似于POSIX中的<code>unlinkat(fd, path, AT_REMOVEDIR)</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_remove_directory_at.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
<li><a name="method_descriptor_remove_directory_at.path"></a><code>path</code>: <code>string</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_remove_directory_at.0"></a> result&lt;_, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_rename_at"></a><code>[method]descriptor.rename-at: func</code></h4>
<p>读取文件系统对象。</p>
<p>注意：其类似于POSIX中的<code>renameat</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_rename_at.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
<li><a name="method_descriptor_rename_at.old_path"></a><code>old-path</code>: <code>string</code></li>
<li><a name="method_descriptor_rename_at.new_descriptor"></a><code>new-descriptor</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
<li><a name="method_descriptor_rename_at.new_path"></a><code>new-path</code>: <code>string</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_rename_at.0"></a> result&lt;_, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_symlink_at"></a><code>[method]descriptor.symlink-at: func</code></h4>
<p>创建符号链接（symbolic link，也称为“symlink”）</p>
<p>如果<code>old-path</code>以<code>/</code>开头，此函数将失败并返回<a href="#error_code.not_permitted"><code>error-code::not-permitted</code></a>。</p>
<p>注意：其类似于POSIX中的<code>symlinkat</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_symlink_at.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
<li><a name="method_descriptor_symlink_at.old_path"></a><code>old-path</code>: <code>string</code></li>
<li><a name="method_descriptor_symlink_at.new_path"></a><code>new-path</code>: <code>string</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_symlink_at.0"></a> result&lt;_, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_unlink_file_at"></a><code>[method]descriptor.unlink-file-at: func</code></h4>
<p>取消链接为不是目录的文件系统对象。</p>
<p>如果路径指向一个目录，则返回<a href="#error_code.is_directory"><code>error-code::is-directory</code></a>。
注意：其类似于POSIX中的<code>unlinkat(fd, path, 0)</code>。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_unlink_file_at.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
<li><a name="method_descriptor_unlink_file_at.path"></a><code>path</code>: <code>string</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_unlink_file_at.0"></a> result&lt;_, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_is_same_object"></a><code>[method]descriptor.is-same-object: func</code></h4>
<p>测试两个描述符是否引用同一个文件系统对象。</p>
<p>在POSIX中，这相当于测试两个描述符是否具有相同的设备号（<code>st_dev</code>）和索引节点号（<code>st_ino</code>或<code>d_ino</code>）。
wasi-filesystem 不公开设备号和索引节点号，因此可以使用这个函数来代替。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_is_same_object.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
<li><a name="method_descriptor_is_same_object.other"></a><code>other</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_is_same_object.0"></a> <code>bool</code></li>
</ul>
<h4><a name="method_descriptor_metadata_hash"></a><code>[method]descriptor.metadata-hash: func</code></h4>
<p>返回由描述符引用的文件系统对象相关元数据的哈希值。</p>
<p>这返回一个由最后修改时间戳和文件大小组成的哈希值，
还可能包括索引节点号、设备号、创建时间戳等在文件修改或替换时可能发生变化的元数据字段。
它还可能包含由实现选择的并且不会其他方式暴露的秘密值。</p>
<p>实现应该提供以下属性：</p>
<ul>
<li>如果文件未被修改或替换，计算得到的哈希值通常不应改变。</li>
<li>如果对象被修改或替换，计算得到的哈希值通常应该改变。</li>
<li>哈希的输入不应该从计算得到的哈希值轻易计算出来。</li>
</ul>
<p>然而，这些都不是必须的。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_metadata_hash.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_metadata_hash.0"></a> result&lt;<a href="#metadata_hash_value"><a href="#metadata_hash_value"><code>metadata-hash-value</code></a></a>, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_descriptor_metadata_hash_at"></a><code>[method]descriptor.metadata-hash-at: func</code></h4>
<p>返回由目录描述符和相对路径引用的文件系统对象相关元数据的哈希值。</p>
<p>这执行与<code>metadata-hash</code>相同的元数据哈希计算。</p>
<h5>Params</h5>
<ul>
<li><a name="method_descriptor_metadata_hash_at.self"></a><code>self</code>: borrow&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;</li>
<li><a name="method_descriptor_metadata_hash_at.path_flags"></a><a href="#path_flags"><code>path-flags</code></a>: <a href="#path_flags"><a href="#path_flags"><code>path-flags</code></a></a></li>
<li><a name="method_descriptor_metadata_hash_at.path"></a><code>path</code>: <code>string</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_descriptor_metadata_hash_at.0"></a> result&lt;<a href="#metadata_hash_value"><a href="#metadata_hash_value"><code>metadata-hash-value</code></a></a>, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="method_directory_entry_stream_read_directory_entry"></a><code>[method]directory-entry-stream.read-directory-entry: func</code></h4>
<p>从<a href="#directory_entry_stream"><code>directory-entry-stream</code></a>中读取单个目录项。</p>
<h5>Params</h5>
<ul>
<li><a name="method_directory_entry_stream_read_directory_entry.self"></a><code>self</code>: borrow&lt;<a href="#directory_entry_stream"><a href="#directory_entry_stream"><code>directory-entry-stream</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_directory_entry_stream_read_directory_entry.0"></a> result&lt;option&lt;<a href="#directory_entry"><a href="#directory_entry"><code>directory-entry</code></a></a>&gt;, <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h4><a name="filesystem_error_code"></a><code>filesystem-error-code: func</code></h4>
<p>尝试从提供的流<a href="#error"><code>error</code></a>中提取与文件系统相关的<a href="#error_code"><code>error-code</code></a>。</p>
<p>流操作返回<a href="#stream_error.last_operation_failed"><code>stream-error::last-operation-failed</code></a>时，其载荷包含有关失败操作的更多信息。
可以将此载荷传递给此函数，以查看是否有关于文件系统的错误信息可返回。</p>
<p>请注意，此函数可能会失败，因为并非所有与流相关的错误都是与文件系统相关的错误。</p>
<h5>Params</h5>
<ul>
<li><a name="filesystem_error_code.err"></a><code>err</code>: borrow&lt;<a href="#error"><a href="#error"><code>error</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="filesystem_error_code.0"></a> option&lt;<a href="#error_code"><a href="#error_code"><code>error-code</code></a></a>&gt;</li>
</ul>
<h2><a name="wasi_filesystem_preopens_0_2_0"></a>Import interface wasi:filesystem/preopens@0.2.0</h2>
<hr />
<h3>Types</h3>
<h4><a name="descriptor"></a><code>type descriptor</code></h4>
<p><a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a></p>
<p>
----
<h3>Functions</h3>
<h4><a name="get_directories"></a><code>get-directories: func</code></h4>
<p>返回预打开的目录集合及其路径。</p>
<h5>Return values</h5>
<ul>
<li><a name="get_directories.0"></a> list&lt;(own&lt;<a href="#descriptor"><a href="#descriptor"><code>descriptor</code></a></a>&gt;, <code>string</code>)&gt;</li>
</ul>
