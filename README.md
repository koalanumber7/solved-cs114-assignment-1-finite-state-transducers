Download Link: https://assignmentchef.com/product/solved-cs114-assignment-1-finite-state-transducers
<br>
This assignment is on finite state transducers and will require you to create transducers in Python. There are two problems, and we have provided some code to get you started, as well as a few utilities that will make it easier for you to debug and test your code.

Some minimal test cases are provided as well. Before you turn in your work, you should at least pass all of the test cases we provided although it is neither sufficient nor necessary for getting full credit. You should add your own test cases to help yourself debug your program.

<h1>Problem 1: Soundex<a href="#_ftn1" name="_ftnref1"><sup><strong>[1]</strong></sup></a></h1>

The Soundex algorithm is a phonetic algorithm commonly used by libraries and the Census Bureau to represent people’s names as they are pronounced in English. It has the advantage that name variations with minor spelling differences will map to the same representation, as long as they have the same pronunciation in English. Here is how the algorithm works:

<strong>Step 1: </strong>Retain the first letter of the name. This may be uppercased or lowercased.

<strong>Step 2: </strong>Remove all <strong>non-initial </strong>occurrences of the following letters: a, e, h, i, o, u, w, y. (To clarify, this step removes all occurrences of the given characters <em>except </em>when they occur in the first position.)

<strong>Step 3: </strong>Replace the remaining letters (except the first) with numbers:

b, f, p, v→ 1 c, g, j, k, q, s, x, z→ 2 d, t→ 3 l→ 4 m, n→ 5 r→ 6

If two or more letters from the same number group were adjacent in the <em>original </em>name, then <em>only </em>replace the first of those letters with the corresponding number and ignore the others. This rule also applies at the beginning of the name: retain the first letter, but ignore the others.

<strong>Step 4: </strong>If there are more than 3 digits in the resulting output, then drop the extra ones.

<strong>Step 5: </strong>If there are less than 3 digits, then pad at the end with the required number of trailing zeros.

The final output of applying Soundex algorithm to any input string should be of the form Letter Digit Digit Digit. Table 1 shows the output of the Soundex algorithm for some example names.

<table width="140">

 <tbody>

  <tr>

   <td width="71"><strong>Input</strong></td>

   <td width="69"><strong>Output</strong></td>

  </tr>

  <tr>

   <td width="71">Jurafsky</td>

   <td width="69">J612</td>

  </tr>

  <tr>

   <td width="71">Jarovski</td>

   <td width="69">J612</td>

  </tr>

  <tr>

   <td width="71">Resnik</td>

   <td width="69">R252</td>

  </tr>

  <tr>

   <td width="71">Reznick</td>

   <td width="69">R252</td>

  </tr>

  <tr>

   <td width="71">Euler</td>

   <td width="69">E460</td>

  </tr>

  <tr>

   <td width="71">Peterson</td>

   <td width="69">P362</td>

  </tr>

  <tr>

   <td width="71">Ashcroft</td>

   <td width="69">A226</td>

  </tr>

  <tr>

   <td width="71">Pfister</td>

   <td width="69">P236</td>

  </tr>

 </tbody>

</table>

Table 1: Example outputs for the Soundex algorithm.

Construct an fst that implements the Soundex algorithm. Obviously, it is non-trivial to implement a single transducer for the entire algorithm. Therefore, the strategy we will adopt is a bottom-up one: implement multiple transducers, each performing a simpler task, and then compose them together to get the final output. One possibility is to partition the algorithm across three transducers:

<ol>

 <li><strong>Transducer 1</strong>: (letters_to_numbers) Performs steps 1-3 of the algorithm, i.e, retaining the first letter, removing letters and replacing letters with numbers.</li>

 <li><strong>Transducer 2</strong>: (truncate_to_three_digits) Performs step 4 of the algorithm, i.e., truncating extra digits.</li>

 <li><strong>Transducer 3</strong>: (add_zero_padding) Performs step 5 of the algorithm, i.e., padding with zeros if required.</li>

</ol>

Note that each of these three transducers will have characters as input/output symbols.

To make things easier for you, we have provided the file soundex.py which is where you will write your code. It already imports all needed modules and functions (including fsmutils.py). It also creates three transducer objects—as dictated by the bottom-up strategy outlined above—such that all you should have to do is to figure out the states and arcs required by each transducer. It also contains code that allows you to input a single name on the command line to get the output.

Note that while we have provided you with sample unit tests containing some names, it might be very useful to test your code on other names. For comparison purposes, you may use one of the many Soundex calculators available online to create more test cases. (Note that some calculators may use additional rules; you only need to implement the rules listed above.)

<h1>Problem 2: English Morphology</h1>

English often requires some spelling changes at the morpheme boundary between the stem and the affixes that indicate the inflection of the verb. Specifically, we will build a finite state transducer to handle the K-insertion rule in English.

If a verb ends with vowel + c, then we add k before adding in the -ed and -ing suffixes. For example:

panicked &#x2194; panic + ed &#x2194; panic+past form panicking &#x2194; panic + ing &#x2194; panic+present participle form lick &#x2194; lick + ed &#x2194; lick+past form lick &#x2194; lick + ing &#x2194; lick+present participle form

As you can see in the examples, the morphological parsing process can be done in two steps (although you may skip the intermediate step). So you might want to (but don’t have to) build two separate FSTs and compose them. The lexicon FST only has to include two inflectional morphemes (-ed and -ing) and five verbs: want, sync, panic, havoc, and lick. The other FST will handle the actual K-insertion rule.

The parser FST will be used as a morphological parser to transduce the surface form (e.g panicked) to the lexical form (panic + past form). The generator FST will be used to transduce the lexical form to the surface form.

<h1>How to use the FST code</h1>

We’ve provided a modified fst implementation from nltk. However, the documentation is less than perfect. Therefore, in this section, we will give you a brief introduction to everything that you will need to understand how to build finite state transducers in Python.

To start building fsts, you need to first import the fst module into your program’s namespace. Then, you need to instantiate an FST object. Once you have such an object, you can start adding states and arcs to it. Listing 1 shows how to build a very simple finite state transducer—one that removes all vowels from any given word.

Feel free to try out the example (provided as devowelizer.py) to see how it works on some of your own input. There are a few points worth mentioning:

<ol>

 <li>The Python string module comes with a few built-in strings that you might be able to use in this assignment for purposes of iteration as used in the example on line 23. These are:</li>

</ol>

string.ascii letters : All letters, upppercased and lowercased string.ascii lowercase : All lowercased letters string.ascii uppercase : All uppercased letters

<ol start="2">

 <li>States can be added to an FST object by using its add state() This method takes a single argument: a unique string identifier for the state. Our example has only one state (line 13). Furthermore, there can only be <strong>one </strong>initial state and this is indicated by assigning the state identifier to the FST object’s initial state field (line 17). However, there may be multiple final states in an FST. In fact, it is almost always necessary to have multiple final states when working with transducers. All final states may be so indicated by using the FST object’s set final() method (line 18).</li>

 <li>Arcs can be added between the states of an FST object by using its add arc() This method takes the following arguments (in order): the starting state, the ending state, the input symbol and, finally, the output symbol.</li>

</ol>

Listing 1: A 1-state transducer that deletes vowels

17 f.initial_state = <sup>’</sup>1<sup>’ </sup>18 f.set_final(<sup>’</sup>1<sup>’</sup>)

20 # Now, we need to add an arc for each letter; if the letter is 21 # a vowel then the transition outputs nothing but otherwise it 22 # outputs the same letter that it consumed.

<ul>

 <li>forletterinstring.ascii_lowercase:</li>

 <li>ifletterinvowels: 25 f.add_arc(<sup>’</sup>1<sup>’</sup>, <sup>’</sup>1<sup>’</sup>, letter, <sup>’’</sup>)</li>

 <li>else:</li>

 <li>f.add_arc(<sup>’</sup>1<sup>’</sup>, <sup>’</sup>1<sup>’</sup>, letter, letter)</li>

 <li># Evaluate it on some example words</li>

 <li>print(<sup>’’</sup>.join(f.transduce([<sup>’</sup>v<sup>’</sup>, <sup>’</sup>o<sup>’</sup>, <sup>’</sup>w<sup>’</sup>, <sup>’</sup>e<sup>’</sup>, <sup>’</sup>l<sup>’</sup>])[0]))</li>

 <li>print(<sup>’’</sup>.join(f.transduce(<sup>’</sup>exception<sup>’</sup>)[0]))</li>

</ul>

<ol start="4">

 <li>An FST object can be evaluated against any input string by using its transduce() method. Here’s how:</li>

</ol>

<ul>

 <li>If your transducer uses <strong>characters </strong>as input/output symbols, then the input to transduce() must be a <strong>list of characters</strong>. You may either directly input a list of characters (line 30) or a string (which is just a list of characters) (lines 31 and 32). Note that the output of transduce() is a <strong>list of possible transductions</strong>. For the assignment, you should make sure your transductions are <strong>deterministic</strong>; i.e., there should be at most one element in the list.</li>

 <li>If your transducer uses <strong>words </strong>as input/output symbols, then the input to transduce() should be a <strong>list of words</strong>. Again, you can either explicitly use a list of words or call the split method on a string of words separated by whitespace. For example, say your FST maps from strings like <em>ten </em>and <em>twenty </em>to number strings <em>10 </em>and <em>20</em>, then to evaluate it on the input string <em>ten twenty</em>, you</li>

</ul>

should use either:

OR

<ol start="5">

 <li>In fsmutils.py , we provide the code for composition. It does not actually perform the composition and return a newly composed FST. It cascades the output from one FST to the next.</li>

</ol>

The above function call computes (<sub>f3 </sub>◦ <sub>f2 </sub>◦ <sub>f1</sub>)(<sub>S</sub>). i.e., it will first  apply transducer f1 to the given input S, use the output of this transduction as input to transducer f2 and so on and so forth. S must be a list of characters. Note that like transduce(), the compose() function returns a list of possible transductions; make sure there is at most one element in the list.

<a href="#_ftnref1" name="_ftn1">[1]</a> This problem is adapted from Jordan Boyd-Graber’s course at UMD.