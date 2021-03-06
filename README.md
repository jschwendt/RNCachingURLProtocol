# BACKGROUND

RNCachingURLProtocol is a simple shim for the HTTP protocol (that’s not
nearly as scary as it sounds). Anytime a URL is downloaded, the response is
cached to disk. Anytime a URL is requested, if we’re online then things
proceed normally. If we’re offline, then we retrieve the cached version.

This fork of RNCachingURLProtocol supports selective caching as well as the 
ability to set staleness and expiration times by mime type.  When a cached item
becomes stale, it will be treated as a cache hit when the device is offline, but
a miss when it's online.  Items will remain in the cache until they expire and
are removed.  A convenience method "removeExpiredCacheItems" can be scheduled and
will clean up expired items on a background thread.

The Whitelist and Blacklist are meant to work together or alone.  When a URL is processed,
it is first matched to see if it exists on the Whitelist.  If the Whitelist is empty,
all URLs are essentially Whitelisted.  Items matched on the Whitelist will then try to be
matched on the Blacklist.  If the item is matched on the Blacklist, it will not be cached.
Assuming it passes through both the Whitelist and Blacklist checks, then it will be cached.
Note that the lists are Regular Expression patterns which must be escaped appropriately.

# USAGE

1. To build, you will need the Reachability code from Apple (included). That requires that you link with
	`SystemConfiguration.framework`.

2. At some point early in the program (usually `application:didFinishLaunchingWithOptions:`),
   call the following:

	`[NSURLProtocol registerClass:[RNCachingURLProtocol class]];`

3. Optionally add Whitelist/Blacklist URLs patterns.  Note that Blacklisted URL patterns are evaluated after Whitelisted patterns are.

	`[RNCachingURLProtocol addWhiteListURLWithPattern:@"github\\.org"];`
	`[RNCachingURLProtocol addWhiteListURLWithPattern:@"wikipedia\\.org"];`
	`[RNCachingURLProtocol addBlackListURLWithPattern:@"upload\\.wikipedia\\.org"];`

4. Optionally remove expired cached items on a regular basis

	`[NSTimer scheduledTimerWithTimeInterval:(5*60) target:[RNCachingURLProtocol class] selector:@selector(removeExpiredCacheItems) userInfo:nil repeats:YES];`

For more details see
   [Drop-in offline caching for UIWebView (and NSURLProtocol)](http://robnapier.net/blog/offline-uiwebview-nsurlprotocol-588).

# EXAMPLE

See the CachedWebView project for example usage.

You can turn debug messages on by commenting out this line in the .h: #define RNCACHING_DISABLE_LOGGING

# LICENSE

 This code is licensed under the MIT License:
 
 Permission is hereby granted, free of charge, to any person obtaining a
 copy of this software and associated documentation files (the "Software"),
 to deal in the Software without restriction, including without limitation
 the rights to use, copy, modify, merge, publish, distribute, sublicense,
 and/or sell copies of the Software, and to permit persons to whom the
 Software is furnished to do so, subject to the following conditions:

 The above copyright notice and this permission notice shall be included in
 all copies or substantial portions of the Software.

 THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
 IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
 FITNESS FOR A PARTICULAR PURPOSE AND NON-INFRINGEMENT. IN NO EVENT SHALL THE
 AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
 LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
 FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER
 DEALINGS IN THE SOFTWARE.
