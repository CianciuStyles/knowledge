# Legacy Code

## Notes

### How to own a code

* Being able to run the application locally on your machine will make the feedback iteration loop faster; you can insert probes, printfs, sniff network traffic, shut down vital parts - it's your own black-box laboratory.
* I fully expect to spend a day or two just getting one application to run when first getting into a new project because of the dependencies, proxies, firewalls and configuration needing to be in place.
* A good way to see if it runs at all after it's been built is to isolate it in a container environment such as docker without a network connection. This way you can start it and watch if any network calls are made. Another way to simply pull the network cord.
* A really good way of learning how the system interacts with other systems is to build rigs and fakes of the real system it's calling. Build the smallest possible fake system with canned responses and wire up the main application to talk to your local mock instead.
* With the application now defused by talking to fake mocks with the internal state known we are ready to make changes. If it wasn't for human psychology!
* If the layout in one file doesn't make sense - move it around until it does. If the names of files, methods or variables are too vague or too specific, change them.

### How to read a code

* Trying to understand a new codebase is trying to get a mental model of the program into your head.
* [Active recall](https://www.wikiwand.com/en/Active_recall): actively trying to recall something will at the same time increase your understanding of it.
* [Generation effect](https://www.wikiwand.com/en/Generation_effect): if you're forced to come up with an answer to a question even when you are dead wrong pays off. By making someone guess the answer to a question even when the subject is totally unknown boosts the retention of the correct answer once it's revealed.
* First three stages of reading a book:
  * First level - elementary reading: the readers reads a book cover to cover
  * Second level - inspectional reading: the user tries to find out the most of a book in a limited time by asking the questin "What is the book about, as a whole?"
  * Third level - active reading: the reader understands the book by bottoming-up from finding the most important words the author uses all the way up to the arguments the author has used.
* If you don't know the syntax of your language then stop right here, learn the syntax and then come back.
* Without looking at the README or similar - just write down what you know about the project beforehand \(which will leverage the generation effect\):
  * What problem does it solve?
  * How would I implement this?
  * What do I know of the domain?
* Create a new folder for notes on the project, and create folders with empty files with the same name as the files in the project you're investigating; then, for each file you create, open it up and write a comment at the top and make some small note on what you **think** this file does.
* Now, return to the code for an inspectional read, with the goal of identiying the major data-structures. Ignore everything else for now as it won't make sense yet. When you find them, copy them over by hand to your own version of the project. Again make comments around the data-structures on what you **think** they mean.
* Now try to build the first mental model of the architecture by grabbing a piece of paper and mapping out the subsystems of the applicaton by only knowing the folder-names, file-names and major data-structures. Thanks to the generation effect merely guessing the architecture will make the correct stick even more.
* For the files you've now deemed important so far, open them up in the original project and fold the entire file down to the method level. Using the same method for the data-structures, copy by hand the method signature to your own project and add a comment on what you **think** the method does.
* By looking at folder-names, file-names, method signatures and data-structures, you will probably have a good feel for what terms are important. Grab a piece of paper and write those terms you think are most important down. Also write the explanation for them. Once done, go back to the architectural drawing and see if it's still up to par with your latest findings.

## Resources

### Articles

* [How to own a code](https://www.iamjonas.me/2020/11/how-to-own-code.html) - Jonas Lundberg
* [How to read a code](https://www.iamjonas.me/2020/08/how-to-read-code.html) - Jonas Lundberg



