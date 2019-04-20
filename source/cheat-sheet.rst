.. _cheat-sheet:

Cheat Sheet
===========

Be sure to give a title to each page.  For example, this page is called **Cheat Sheet**.  When linking to this page, the title text appears as the title of the document.

.. code-block:: cpp
    :linenos:
    :caption: This is a caption
    :name: This is a name

    #include <iostream>

    int main (int argc, char* argv[])
    {
        std::cout << "Hello World" << std::endl;
        return 0;
    }

* This is *italicized*
* This is **bolded**
* This is ``verbatim``
  item uses two lines.

  * this is a sub list
  * also known as a nested list

* and here the parent list continues

1. This is a numbered list.
2. It has two items too.

#. This is a numbered list.
#. It has two items too.

:fieldname: Field content

* This link_ is an alias to the link below.
* For multi-worded links, `define them like this`_.
* Define links inline `like this <http://www.python.org>`_.
* This is a implicit link to an existing document `cheat-sheet`_

This is a line with a footnote.\ [#label]_

.. [#label] This is the footnote content.
.. _define them like this: http://amazon.com
.. _link: http://google.com