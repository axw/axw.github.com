+++
title = "Pushy Public Documentation"
date = 2010-08-07T13:40:00Z
updated = 2010-08-07T13:40:30Z
blogimport = true 
aliases = [

  "/2010/08/pushy-public-documentation.html",

]
[author]
	name = "Andrew Wilkins"
	uri = "https://plus.google.com/102738380796586573408"
+++

So, I finally got around to writing some proper <a href="http://packages.python.org/pushy/">documentation for Pushy</a>.<br /><br />I had originally used <a href="http://epydoc.sourceforge.net/">Epydoc</a> to extract docstrings, and generate API documents, which I have been hosting. Then I realised I could publish HTML to <a href="http://pypi.python.org/pypi">PyPI</a>, so I thought I'd do something a little more friendly than presenting the gory details of the API.<br /><br />In the past I've used&nbsp;<a href="http://www.methods.co.nz/asciidoc/">Asciidoc</a>,&nbsp;a lightweight markup language, in the vein of Wiki markup languages. I found Asciidoc fairly simply to write, and there is a standard tool for processing and producing various output, including of course HTML. I wanted to make my documentation to have the look and feel of the Python standard library, so I've been looking into&nbsp;<a href="http://docutils.sourceforge.net/rst.html">reStructuredText</a>.<br /><br />I have to say that reStructuredText is very easy to learn, and&nbsp;<a href="http://sphinx.pocoo.org/">Sphinx</a>, which is the processing tool used to generate the HTML output for the Python documentation, is a pleasure to use. The format of reStructuredText is similar to that of Asciidoc. So far I don't have any particular affinity to either - I mainly went with reStructuredText/Sphinx for the Python documentation theming.
