+++
title = "C-Preprocessor Macros in Python"
date = 2011-06-11T17:50:00Z
updated = 2011-06-11T17:50:38Z
blogimport = true 
aliases = [

  "/2011/06/c-preprocessor-macros-in-python.html",

]
[author]
	name = "Andrew Wilkins"
	uri = "https://plus.google.com/102738380796586573408"
+++

TL;DR: I've started a new project, <a href="https://github.com/axw/csnake">csnake</a>,&nbsp;which allows you to write your C preprocessor macros in Python.<br /><br />Long version ahead...<br /><br /><span class="Apple-style-span" style="font-size: large;"><b>You want to do what now?</b></span><br /><br />I had this silly idea a couple of years ago, to create a C preprocessor in which macros can be defined in Python. This was borne out of me getting sick of hacking build scripts to generate code from data, but pursued more for fun.<br /><br />I started playing around with <a href="http://www.boost.org/doc/libs/1_46_1/libs/wave/index.html">Boost Wave</a>, which is a <i>"Standards conformant, and highly configurable implementation of the mandated C99/C++ preprocessor functionality packed behind an easy to use iterator interface"</i>. With a little skulduggery coding, I managed to define macros as C++ callable objects taking and returning tokens. Then it was a simple matter of adding a Python API.<br /><br /><br /><b><span class="Apple-style-span" style="font-size: large;">The Result</span></b><br /><br />What we end up with is a Python API that looks something like this:<br /><br /><pre class="brush:py">import sys<br />from _preprocessor import *<br />def factorial(n):<br />    import math<br />    return [Token(T_INTLIT, math.factorial(int(str(n))))]<br /><br />p = Preprocessor("test.cpp")<br />p.define(factorial)<br />for t in p.preprocess():<br />    sys.stdout.write(str(t))<br />sys.stdout.write("\n")<br /></pre><br />Which will take...<br /><br /><pre class="brush:cpp">int main(){return factorial(3);}<br /></pre><br />And give you...<br /><br /><pre class="brush:cpp">int main(){return 6;}<br /></pre><br />If it's not immediately clear, it will translate "factorial(<int literal="">)" into an integer literal of the factorial of the input token. This isn't a very interesting example, so if you can imagine a useful application, let me know ;)</int><br /><br />The above script will work with the current code, using Python 3.2, compiling with GCC. If you'd like to play with it, grab the code from the <a href="https://github.com/axw/csnake">csnake github repository</a>. Once you've got it, run "python3.2 setup.py build". Currently there is just an extension module ("csnake._preprocessor"), so set your PYTHONPATH to the build directory and play with that directly.<br /><br />I have chosen to make csnake Python 3.2+ only, for a couple of major reasons:<br /><br /><ul><li>All the cool kids are doing it: it's the way of the future. But seriously, Python 3.x needs more projects to become more mainstream.</li><li>Python 3.2 implements&nbsp;<a href="http://www.python.org/dev/peps/pep-0384/">PEP 384</a>, which allows extension modules to be used across Python versions. <i>Finally.</i>&nbsp;I always hated that I had to recompile for each version.</li></ul><div>... and one very selfish (but minor) reason: I wanted to modernise my Python knowledge. I've been ignoring Python 3.x for far too long.</div><div><br /></div><br /><br /><span class="Apple-style-span" style="font-size: large;"><b>The Road Ahead</b></span><br /><br />What I've done so far is very far from complete, and not immediately useful. It may never be very useful. But if it is to be, it would require at least:<br /><ul><li>A way of detecting (or at least configuring) pre-defined macros and include paths for a target compiler/preprocessor. A standalone C preprocessor isn't worth much. It needs to act like or delegate to a real preprocessor, such as GCC.</li><li>A #pragma to define Python macros in source, or perhaps if I'm feeling adventurous, something like #pydefine.</li><li>A simple, documented Python API.</li><li>A simple command line interface with the look and feel of a standard C preprocessor.</li><li>Some unit tests.</li></ul><div>I hope to add these in the near future. I've had code working for the first two points, and the remaining points are relatively simple. I will post again when I have made some significant progress.</div>