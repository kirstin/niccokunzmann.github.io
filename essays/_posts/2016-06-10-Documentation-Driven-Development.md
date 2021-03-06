---
layout: post
title: Documentation-Driven Development
language: en
---

<iframe width="560" height="315" 
        src="https://www.youtube.com/embed/x5rGUqRWlK8" frameborder="0" 
        allowfullscreen>
</iframe>

I stumbled upon this PyCon2016 talk. Whereas test-driven development is feeling
 the love of many people, I got reminded what it is really about: documenting
 behavior in code. So when I read the title I was inclined to listen to the 
 talk. Only 26 views seem little for what it is: taking TDD to the next level.

There is some truth in it:

> [...] understand information as the process of informing - as an activity. 
> Understand documentation as a process of documenting [...] 
> [[Minute 13 Second 13](https://youtu.be/x5rGUqRWlK8?t=13m13s)] 

What can your Project do?
-------------------------

From the talk...

- It has to do with attitudes.
- Structure documentation well. 
- There is a documentation about how the documentatoin works
  - tutorial
  - how to
  - reference
  - topics
- Make documentation policies as rigorous as your code policies.
- Attend a writethedocs.org workshop.
- Have a documentation manager.
- Spend monay and time on documentation.
- Documentation becomes a movement.

What I take from it
-------------------

Documentation is often seen as something noone likes. But with the Django 
Project we can see that documentation actually drives development as it gets 
new people in and (in)forms the commuity.

When you write your code and you test it, you can do it to be the communication 
medium between the code and the test as you alter both while implementing the 
tests. I write my tests to think about how I would use the code instead in 
contrast to wanting my behavior implemented in an easy way.

Let's think DDD further: 
What is missing when I write my tests is the intention
behind the test and the overall picture. 
Also, tests are not right where the code lives. 
If I were new to the project, I would look for those intentions. 
The intentions should be found in the docs.
Even if the tests were to be found right next to the function, their focus is
not on the humans mental model and the bigger picture but on the specific
behavior. 
Sometimes tests can and should be split up - it could be so much 
that the bigger picture is hard to find.

This is why I think that code, tests, documentation go hand in hand.

Fastforward -> "Documentation First" applied
--------------------------------------------

I really like doing documentation first.

First, I created this class:

    class InstructionToSVG(object):
        """This class maps instructions to SVGs."""
        
        def __init__(self):
            """Create a InstructionToSVG object."""

        @property
        def load(self):
            """Returns a loader object that allows loading SVG file from 
            various sources such as files and folders.
            
            `load.path(path)` loads an svg from a file named path
            `load.folder(path)` loads all SVG files for instructions in the folder
            recursively.
            If multiple files have the same name, the last occurrence is used."""
        
        def instruction_to_svg(self, instruction):
            """Returns an SVG representing the instruction.
            
            The SVG file is determined by the type of the instruction. 
            An instruction of type "knit" will be looked for in a file named
            "knit.svg".
            The SVGs will be scaled to width and heigth of the instruction.
            The color of the instruction is taken into account.
            
            If nothing was loaded to display this instruction, a default image will
            be generated by `default_instruction_to_svg`."""

This was my mental model of how to render instructions to SVG. 
Then, I started writing some tests and they were much better structured than
they usually are. 
It also came to be that I renamed a test method because it just did not 
represent what it should test. 
This was kind of mind-opening.
Talking with the documentation about the tests had the following  implications:

- Tests are better structured
- I focus on the mental model now, not on how to test the mental model
- The tests are more complete as I do not forget edge cases.

I come to think that documentation-first has the same implications on tests
as test-first has on code.

Here is the test structure which would normally be just a lot of functions:

    class TestHasSVGForInstruction(object):
        """This tests the `InstructionToSVG.has_instruction_to_svg` method."""
        # ...
        
        
    class TestDefaultInstrucionToSVG(object):
        """This tests the `InstructionToSVG.default_instruction_to_svg` method.
        """
        # ...

While writing the tests I started adding new documentation for the methods.
This also works like a TODO-list of what needs to be done -
which I would certainly loose track of while implementing tests.
I created new methods:

    class InstructionToSVG(object):
        # ...
        
        def has_svg_for_instruction(self, instruction):
            """Returns whether there is an image for the instruction. 
            
            This can be used before `instruction_to_svg` as it determines whether
            - the default value is used (`False`) 
            - or there is a dedicated svg representation (`True`). 
            """
            
        def default_instruction_to_svg(self, instruction):
            """As `instruction_to_svg()` but it does not take the loaded files into
            account.
            
            In case no file is found for an instruction in `instruction_to_svg()`, 
            this method is used to determine the default svg for it.
            
            The content is created by replacing the text {instruction.type} a the 
            whole svg file named `default.svg`.
            
            If no file `default.svg` was loaded, an empty string is returned."""

These methods have a lot of things that I expect from them.
An with the process

1. document
2. test
3. implement

I am sure 

- not to forget anything or
- that someone notices that a feature was forgotten but intended

Now, I have a lot of undocumented tests lying around which feels as bad as
having untested code.

Roadmap
-------

I am looking forward to using [Sphinx](http://www.sphinx-doc.org).
With its power I would like to add to each method

- how it is tested and therewith,
- how it can be used.

Documentation Coverage / Specification Coverage
-----------------------------------------------

We have test-coverage but we do not have documentation coverage, or do we?
There is old research about it:

- [A SPECIFICATION-BASED COVERAGE METRIC TO EVALUATE TEST SETS
  ](http://www.worldscientific.com/doi/abs/10.1142/S0218539301000530)

Research behind a pay wall. Certainly not what the author intended.
  
And, there is a test tool:

- [docstring-coverage](https://pypi.python.org/pypi/docstring-coverage/0.3.4)

But there is no specification coverage tool like I wish to have it, it seems
[[Testing Tools for Python
](https://wiki.python.org/moin/PythonTestingToolsTaxonomy)].

What I would like to have is:

- which function is tested by which test, see that in the documentation. 
  - This could be like a test: the test function should be referenced in the doc string
  - This may only need to apply for tests that call this function "directly", meaning out of their module
- coverage of what needs to be documented
- Each paragraph in a doc string usually describes a feature. Tests should be listed below that paragraph to look up how this feature is tested and used.
- Sphinx makes it possible to show the source code and to link to the tests.
