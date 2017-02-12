+++
title = "Inline Python macros in C"
date = 2011-06-18T16:38:00Z
updated = 2011-06-18T16:38:00Z
blogimport = true 
aliases = [

  "/2011/06/inline-python-macros-in-c.html",

]
[author]
	name = "Andrew Wilkins"
	uri = "https://plus.google.com/102738380796586573408"
+++

I had a bit of time today to do some more work on that which is soon to be called something other than csnake. I've added a couple of features:<br /><br /><ul><li>You can now define custom pragmas, providing a Python handler function. Unfortunately Boost Wave, which csnake uses for preprocessing, only provides callbacks for pragmas that start with "#pragma wave".</li><li>Built-in pragmas and macros to support defining Python macros inline in C/C++ code.</li><li>A __main__ program in the csnake package. So you can now do "python3.2 -m csnake <source file=""></source>", which will print out the preprocessed source.</li></ul><div>So for example, you can do something like as follows, entirely in one C++ file:</div><br /><div><pre class="brush:c++">// factorial macro.<br />py_def(factorial(n))<br />    import math<br />    f = math.factorial(int(str(n)))<br />    return [Token(T_INTLIT, f)]<br />py_end<br /><br />int main()<br />{<br />    std::cout &lt;&lt; factorial(3) &lt;&lt; std::endl;<br />    return 0;<br />}<br /></pre></div><div><br />This works as follows: <i>py_def</i> and <i>py_end</i>&nbsp;are macros, which in turn use the _Pragma operator with built-in pragmas. Those pragmas are handled by csnake, and signal to collect the tokens in between. When the py_end macro is reached, the tokens are concatenated and a Python function macro is born.<br /><br />I'm intending to do additonal "Python blocks", including at least a <i>py_for</i>&nbsp;block, which will replicate the tokens within the block for each iteration of a loop.<br /><br />There's one big problem with the <i>py_def</i>&nbsp;support at the moment, which is that the tokens go through the normal macro replacement procedure. I think I'll have a fix for that soon.</div>
