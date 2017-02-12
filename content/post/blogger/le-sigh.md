+++
title = "Le sigh"
date = 2011-06-21T22:32:00Z
updated = 2011-06-21T22:32:06Z
blogimport = true 
aliases = [

  "/2011/06/le-sigh.html",

]
[author]
	name = "Andrew Wilkins"
	uri = "https://plus.google.com/102738380796586573408"
+++

I've been coming across more problems with Boost Wave. The current ones blocking me are:<br /><ul><li>A lack of an efficient way to conditionally disable a macro. The "context policy" provides hooks for handling macro expansions, and its return value is meant to control whether the expansion takes place. It doesn't work. I'll write up a bug report when I get some time.</li><li>Wave isn't very forgiving about integer under/overflow. For example, the GNU C library's header "/usr/include/bits/wchar.h" has the following tidbit to determine the sign of wide characters, which Boost Wave barfs on:</li></ul><pre class="brush:c">#elif L'\0' - 1 &gt; 0</pre><br /><div>I think the latter "problem" might actually be reasonable - I believe the standards say that handling of overflow is undefined, and preprocessor/compiler-specific. That doesn't help me much though. I could fix this by writing code to parse the expressions, which seems silly, or by passing off to the target preprocessor (e.g. GCC), which seems like overkill.<br /><br />I'm going to have a look at how hard it would be to use LLVM/Clang's preprocessor instead. If that's a better bet, I may go that way. Otherwise, it might be time to get approval to send patches to the Wave project.</div>
