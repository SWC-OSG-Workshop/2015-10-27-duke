---
layout: lesson
root: ../..
---

## Analyzing Data with NumPy


<div class="">

<p>
We are studying inflammation in patients who have been given a new treatment for arthritis, and need to analyze the first dozen data sets. The data sets are stored in <a href="../../gloss.html#csv">comma-separated values</a> (CSV) format.  Each row holds data for a single patient.  The columns represent arthritis inflammations per day on successive days. The first few rows of our first file look like this:
</p>

<div class="in">
<pre><code>0,0,1,3,1,2,4,7,8,3,3,3,10,5,7,4,7,7,12,18,6,13,11,11,7,7,4,6,8,8,4,4,5,7,3,4,2,3,0,0
0,1,2,1,2,1,3,2,2,6,10,11,5,9,4,4,7,16,8,6,18,4,12,5,12,7,11,5,11,3,3,5,4,4,5,5,1,1,0,1
0,1,1,3,3,2,6,2,5,9,5,7,4,5,4,15,5,11,9,10,19,14,12,17,7,12,11,7,4,2,10,5,4,2,2,3,2,2,1,1
0,0,2,0,4,2,2,1,6,7,10,7,9,13,8,8,15,10,10,7,17,4,4,7,6,15,6,4,9,11,3,5,6,3,3,4,2,3,2,1
0,1,1,3,3,1,3,5,2,4,4,7,6,5,3,10,8,10,6,17,9,14,9,7,13,9,12,6,7,7,9,6,3,2,2,4,2,0,1,1</code></pre>
</div>

<p>
The master copy of the data for this study is in a git repository. You can browse that repository here: <a href="https://github.com/SWC-OSG-Workshop/ExampleData">https://github.com/SWC-OSG-Workshop/ExampleData</a>.  Let's take a look.  Inside the <code>Python</code> folder are several CSV files.  We'll use them in the upcoming examples.
</p>

<p>
N.B. You can also get a copy of this data in your OSG Connect account by <i>cloning</i> it from github:
</p>

<pre> 
$ git clone https://github.com/SWC-OSG-Workshop/ExampleData.git
</pre> 

</div>

<div class="">
<p>We want to:</p>
<ul>
<li>load the CSV data into memory,</li>
<li>calculate the average inflammation per day across all patients, and</li>
<li>plot the result.</li>
</ul>
<p>To do all that, we'll learn a little bit about programming in Python.</p>
</div>

<p>
IPython Notebook is a useful tool for interactive data exploration and visualization.  An IPython server runs the code, and you interact with it through a web browser.  OSG Connect hosts an IPython server for training here: <a href="https://ipython.osgconnect.net/">https://ipython.osgconnect.net</a>.  Let's go there now.  (You will need to log in.)
</p>

<p>
Once your IPython Notebook application opens, you'll see a list of folders:
<ul>
<li><code>data</code></li>
<li><code>ExampleData</code></li>
<li><code>IPython-Examples</code></li>
<li><code>SWC-Python</code></li>
<li><code>users</code></li>
</ul>
</p>

<p>
<code>ExampleData</code> is a copy of the ExampleData repository mentioned previously.  Click on <code>ExampleData</code>, then on <code>Python</code> to "move into" this folder.  You won't see the <code>.csv</code> files because they're not Notebook files, but they are present.
</p>

<p>
Now open a new notebook by clicking on <b>New Notebook</b> in the upper right corner of your IPython Notebook screen.
</p>

<p>
IPython Notebook follows a command/response pattern that may be familiar to you. In each box marked "In", you will enter Python code.  You can enter multiple lines of code in Notebook.  When you are done, press <b>SHIFT+ENTER</b> to execute the code you entered. If any output is produced, or if the code you entered returns a value, it will be shown in a box marked "Out".
</p>

<p>
We'll try this pattern now with a few trivial commands. After each command, press SHIFT+ENTER.
</p>


<div class="in">
<pre>
print "hello"
3+5
max(9, 4, 21, 12)
sum(9, 4, 21, 12)
x1, y1 = 1, 1
x2, y2 = 9, 4
slope = (y2 - y1) / (x2 - x1)
slope
</pre>
</div>

<hr/>

<div class="objectives">
<h4 id="objectives">Objectives</h4>
<ul>
<li>Explain what a library is, and what libraries are used for.</li>
<li>Load a Python library and use the things it contains.</li>
<li>Read tabular data from a file into a program.</li>
<li>Assign values to variables.</li>
<li>Select individual values and subsections from data.</li>
<li>Perform operations on arrays of data.</li>
<li>Display simple graphs.</li>
</ul>
</div>

### Libraries

<div class="">

<p>
The essence of practical programming &mdash; as with mathematics &mdash; is learning to use fundamental tools and techniques to accomplish more complex tasks, without feeling overwhelmed by the magnitude of the greater task.
</p>

<p>
Many of powerful tools are built into the core of languages like Python, but as with a volume of mathematical proofs, even more power resides in the <a href="../../gloss.html#library">libraries</a> that developers build.  Different tasks that these libraries <i>implement</i> can be combined sequentially or conditionally to produce programs that do a lot of work, but with relatively simple rules.
</p>

<p>
In Python, a library is <i>loaded</i> using the <a href="../../gloss.html#import">import</a> statement.  Let's do a simple demonstration by looking for the files in the current folder.  (Recall from the section on shell programming that <code>.</code> represents the current directory.)  Type the following:
</p>

<div class="in">
<pre>
import os
os.listdir('.')
</pre>
</div>

<p>
<i>os</i> is a library that implements some useful procedures for interacting with the operating system.  <i>listdir</i> is a function, residing in the <i>os</i> library (or "module") which finds all the files in a directory.  It returns these as a list that you can assign to a variable, or just display on the screen.
</p>

</div>

### Loading Data

<div class="">

<p>
In order to work with our inflammation data, we will import a library called NumPy that knows how to operate on matrices:
</p>

<div class="in">
<pre>import numpy</pre>
</div>


<div class="">
<p>Importing a library is like getting a piece of lab equipment out of a storage locker and setting it up on the bench. NumPy in particular knows how to read and interpret CSV files, so we can ask it to load our data directly from a file:</p>
</div>


<div class="in">
<pre>numpy.loadtxt(fname=&#39;inflammation-01.csv&#39;, delimiter=&#39;,&#39;)</pre>
</div>

<div class="out">
<pre>array([[ 0.,  0.,  1., ...,  3.,  0.,  0.],
       [ 0.,  1.,  2., ...,  1.,  0.,  1.],
       [ 0.,  1.,  1., ...,  2.,  1.,  1.],
       ..., 
       [ 0.,  1.,  1., ...,  1.,  1.,  1.],
       [ 0.,  0.,  0., ...,  0.,  2.,  0.],
       [ 0.,  0.,  1., ...,  1.,  1.,  0.]])</pre>
</div>

<div class="">

<p>
The expression <code>numpy.loadtxt(...)</code> is a <a href="../../gloss.html#function-call">function call</a> that asks Python to run the function <code>loadtxt</code> that belongs to the <code>numpy</code> library. This <a href="../../gloss.html#dotted-notation">dotted notation</a> is used everywhere in Python to refer to the parts of things as <code>whole.part</code>.
</p>

<p>
<code>numpy.loadtxt</code> has two <a href="../../gloss.html#parameter">parameters</a>: the name of the file we want to read, and the <a href="../../gloss.html#delimiter">delimiter</a> that separates values on a line. These both need to be character strings (or <a href="../../gloss.html#string">strings</a> for short), so we put them in quotes.
</p>

<p>
When we are finished typing and press Shift+Enter, the notebook runs our command. Since we haven't told it to do anything else with the function's output, the notebook displays it. In this case, that output is the data we just loaded. By default, only a few rows and columns are shown (with <code>...</code> displayed to mark missing data). To save space, Python displays numbers as <code>1.</code> instead of <code>1.0</code> when there's nothing interesting after the decimal point.
</p>

<p>
If you want to suppress the output, insert ";" at the end of the statement. 
</p>

<div class="in">
<pre>numpy.loadtxt(fname=&#39;inflammation-01.csv&#39;, delimiter=&#39;,&#39;);</pre>
</div>

<p>
IPython Notebook continuously saves your work, but you can ask it to save at any time by typing Command-S (Mac) or Control-S (Windows).
</p>

</div>


<div class="">
<p>Our call to <code>numpy.loadtxt</code> read our file, but didn't save the data in memory. To do that, we need to <a href="../../gloss.html#assignment">assign</a> the array to a <a href="../../gloss.html#variable">variable</a>. A variable is just a name for a value, such as <code>x</code>, <code>current_temperature</code>, or <code>subject_id</code>. We can create a new variable simply by assigning a value to it using <code>=</code>:</p>
</div>


<div class="in">
<pre>weight_kg = 55</pre>
</div>


<div class="">
<p>Once a variable has a value, we can print it:</p>
</div>


<div class="in">
<pre>print weight_kg</pre>
</div>

<div class="out">
<pre>55</pre>
</div>


<div class="">
<p>and do arithmetic with it:</p>
</div>


<div class="in">
<pre>print &#39;weight in pounds:&#39;, 2.2 * weight_kg</pre>
</div>

<div class="out">
<pre>weight in pounds: 121.0</pre>
</div>


<div class="">
<p>We can also change a variable's value by assigning it a new one:</p>
</div>


<div class="in">
<pre>weight_kg = 57.5
print &#39;weight in kilograms is now:&#39;, weight_kg</pre>
</div>

<div class="out">
<pre>weight in kilograms is now: 57.5
</pre>
</div>


<div class="">
<p>As the example above shows, we can print several things at once by separating them with commas.</p>
</div>

<p>In Python, as in most other common programming languages, <i>assignment</i> refers to the linking of a <i>name</i> (also called a variable, symbol, or label) to a <i>value</i>.  It's a simple one-way relation.
Assigning a value to one variable does <em>not</em> change the values of other variables.
</p>

<p>
For example, let's now store the subject's weight in pounds in a variable:
</p>


<div class="in">
<pre>weight_lb = 2.2 * weight_kg
print &#39;weight in kilograms:&#39;, weight_kg, &#39;and in pounds:&#39;, weight_lb</pre>
</div>

<div class="out">
<pre>weight in kilograms: 57.5 and in pounds: 126.5
</pre>
</div>


<div class="">
<p>Now let's change <code>weight_kg</code>.</p>
</div>


<div class="in">
<pre>weight_kg = 100.0
print &#39;weight in kilograms is now:&#39;, weight_kg, &#39;and weight in pounds is still:&#39;, weight_lb</pre>
</div>

<div class="out">
<pre>weight in kilograms is now: 100.0 and weight in pounds is still: 126.5
</pre>
</div>

<p>
Even though we initially defined <code>weight_lb</code> in terms of <code>weight_kg</code>, that relation does not persist, and <code>weight_lb</code> doesn't change when we redefine <code>weight_kg</code>. The conversion of weight_kg to weight_lb happens <i>at the moment</i> that it's assigned, and never again after. The assignment itself is binding.  This may feel unnatural, especially if you're used to working with spreadsheets.  In a spreadsheet the only way to define one cell in terms of another is through a <i>function</i>.  Python has functions too, but they don't look quite the same as assignments.  We'll go over these later.
</p>

<p>Now that we know how to assign things to variables, let's re-run <code>numpy.loadtxt</code> and save its result:</p>
</div>


<div class="in">
<pre>data = numpy.loadtxt(fname=&#39;inflammation-01.csv&#39;, delimiter=&#39;,&#39;)</pre>
</div>


<div class="">
<p>This statement doesn't produce any output because assignment doesn't <i>return</i> anything &mdash; the value previously returned and displayed is now bound up in the variable <code>data</code>.. If we want to check that our data has been loaded, we can print the variable's value:</p>
</div>


<div class="in">
<pre>print data</pre>
</div>

<div class="out">
<pre>[[ 0.  0.  1. ...,  3.  0.  0.]
 [ 0.  1.  2. ...,  1.  0.  1.]
 [ 0.  1.  1. ...,  2.  1.  1.]
 ..., 
 [ 0.  1.  1. ...,  1.  1.  1.]
 [ 0.  0.  0. ...,  0.  2.  0.]
 [ 0.  0.  1. ...,  1.  1.  0.]]
</pre>
</div>


### Manipulating Data

<div class="">
<p>Now that our data is in memory, we can start doing things with it. First, let's ask what <a href="../../gloss.html#data-type">type</a> of thing <code>data</code> refers to:</p>
</div>


<div class="in">
<pre>print type(data)</pre>
</div>

<div class="out">
<pre>&lt;type &#39;numpy.ndarray&#39;&gt;
</pre>
</div>


<div class="">
<p>The output tells us that <code>data</code> currently refers to an N-dimensional array created by the NumPy library. We can see what its <a href="../../gloss.html#shape">shape</a> is like this:</p>
</div>


<div class="in">
<pre>print data.shape</pre>
</div>

<div class="out">
<pre>(60, 40)
</pre>
</div>


<div class="">
<p>This tells us that <code>data</code> has 60 rows and 40 columns. <code>data.shape</code> is a <a href="../../gloss.html#member">member</a> of <code>data</code>, i.e., a value that is stored as part of a larger value. We use the same dotted notation for the members of values that we use for the functions in libraries because they have the same part-and-whole relationship.</p>
</div>


<div class="">
<p>If we want to get a single value from the matrix, we must provide an <a href="../../gloss.html#index">index</a> in square brackets, just as we do in math:</p>
</div>


<div class="in">
<pre>print &#39;first value in data:&#39;, data[0, 0]</pre>
</div>

<div class="out">
<pre>first value in data: 0.0
</pre>
</div>


<div class="in">
<pre>print &#39;middle value in data:&#39;, data[30, 20]</pre>
</div>

<div class="out">
<pre>middle value in data: 13.0
</pre>
</div>


<div class="">
<p>The expression <code>data[30, 20]</code> may not surprise you, but <code>data[0, 0]</code> might. Programming languages like Fortran and MATLAB start counting at 1, because that's what humans are most accustomed to. Languages in the C family (including C++, Java, Perl, and Python) count from 0 because it's simpler for computers to do, which in turn makes it easier for programmers to sequence multiple steps that deal with the same arrays. (There's a lot less adding or subtracting 1 overall.) As a result, if we have an M×N array in Python, its indices go from 0 to M-1 on the first axis and 0 to N-1 on the second. It takes a bit of getting used to, but one way to remember the rule is that the index is how many steps we have to take from the start to get the item we want.</p>

<blockquote>
<h4>In the Corner</h4>
<p>What may also surprise you is that when Python displays an array, it shows the element with index <code>[0, 0]</code> in the upper left corner rather than the lower left. This is consistent with the way mathematicians draw matrices, but different from the Cartesian coordinates. The indices are (row, column) instead of (column, row) for the same reason.</p>
</blockquote>
</div>


#### Ranges

Python has a standard <i>range notation</i> used to denote
<a href="../../gloss.html#slice"><i>slices</i></a>
or <i>subsets</i> of a set. Any array in Python can be sliced using range notation.  As an example, suppose we have a set of children's ages:

<div class="in">
<pre>
ages = [2, 3, 6, 9, 11, 13, 17]
</pre>
</div>

<p>
Range notation causes <code>ages[1:4]</code> to refer to elements 1, 2, and 3 of the list &mdash; the slice starting at index 1 and extending to (but not including) index 4 &mdash; or <code>[3, 6, 9]</code>.  The <code>1</code> is the index of the start of the range, and the <code>4</code> &mdash; counterintuitively &mdash;is the index of the item <i>after</i> the last item in the subset.  So elements N through M of a set named "sequence" would be indicated as <code>sequence[<b>N</b>:<b>M+1</b>]</code>.  It is also possible to leave out the <b>N</b> or <b>M</b> values.
</p>

<p>
Since character strings are a kind of array, this is most easily demonstrated using strings:
</p>

<div class="in">
<pre>
soup = "french onion"
print soup[4:9]
print soup[:6]
print soup[7:]
</pre>
</div>


#### NumPy's N-Dimensional Ranges

<div class="">
<p>An index like <code>[30, 20]</code> selects a single element of an array, but we can select whole sections as well, using an extended form of range notation. For example, we can select the first ten days (columns) of values for the first four (rows) patients like this:</p>
</div>


<div class="in">
<pre>print data[0:4, 0:10]</pre>
</div>

<div class="out">
<pre>[[ 0.  0.  1.  3.  1.  2.  4.  7.  8.  3.]
 [ 0.  1.  2.  1.  2.  1.  3.  2.  2.  6.]
 [ 0.  1.  1.  3.  3.  2.  6.  2.  5.  9.]
 [ 0.  0.  2.  0.  4.  2.  2.  1.  6.  7.]]
</pre>
</div>


<div class="">
<p>The <a href="../../gloss.html#slice">slice</a> <code>0:4</code> means, &quot;Start at index 0 and go up to, but not including, index 4.&quot; Again, the up-to-but-not-including takes a bit of getting used to, but the rule is that the difference between the upper and lower bounds is the number of values in the slice.</p>
<p>We don't have to start slices at 0:</p>
</div>


<div class="in">
<pre>print data[5:10, 0:10]</pre>
</div>

<div class="out">
<pre>[[ 0.  0.  1.  2.  2.  4.  2.  1.  6.  4.]
 [ 0.  0.  2.  2.  4.  2.  2.  5.  5.  8.]
 [ 0.  0.  1.  2.  3.  1.  2.  3.  5.  3.]
 [ 0.  0.  0.  3.  1.  5.  6.  5.  5.  8.]
 [ 0.  1.  1.  2.  1.  3.  5.  3.  5.  8.]]
</pre>
</div>


<div class="">
<p>and we don't have to take all the values in the slice---if we provide a <a href="../../gloss.html#stride">stride</a>, Python takes values spaced that far apart:</p>
</div>


<div class="in">
<pre>print data[0:10:3, 0:10:2]</pre>
</div>

<div class="out">
<pre>[[ 0.  1.  1.  4.  8.]
 [ 0.  2.  4.  2.  6.]
 [ 0.  2.  4.  2.  5.]
 [ 0.  1.  1.  5.  5.]]
</pre>
</div>


<div class="">
<p>Here, we have taken rows 0, 3, 6, and 9, and columns 0, 2, 4, 6, and 8. (Again, we always include the lower bound, but stop when we reach or cross the upper bound.)</p>
</div>


<div class="">
<p>We also don't have to include the upper and lower bound on the slice. If we don't include the lower bound, Python uses 0 by default; if we don't include the upper, the slice runs to the end of the axis, and if we don't include either (i.e., if we just use ':' on its own), the slice includes everything:</p>
</div>


<div class="in">
<pre>small = data[:3, 36:]
print &#39;small is:&#39;
print small</pre>
</div>

<div class="out">
<pre>small is:
[[ 2.  3.  0.  0.]
 [ 1.  1.  0.  1.]
 [ 2.  2.  1.  1.]]
</pre>
</div>


<div class="">
<p>Arrays also know how to perform common mathematical operations on their values. If we want to find the average inflammation for all patients on all days, for example, we can just ask the array for its mean value.</p>
</div>


<div class="in">
<pre>print data.mean()</pre>
</div>

<div class="out">
<pre>6.14875
</pre>
</div>


<div class="">
<p><code>mean</code> is a <a href="../../gloss.html#method">method</a> of the array, i.e., a function that belongs to it in the same way that the member <code>shape</code> does. If variables are nouns, methods are verbs: they are what the thing in question knows how to do. This is why <code>data.shape</code> doesn't need to be called (it's just a thing) but <code>data.mean()</code> does (it's an action). It is also why we need empty parentheses for <code>data.mean()</code>: even when we're not passing in any parameters, parentheses are how we tell Python to go and do something for us.</p>
<p>NumPy arrays have lots of useful methods:</p>
</div>


<div class="in">
<pre>print &#39;maximum inflammation:&#39;, data.max()
print &#39;minimum inflammation:&#39;, data.min()
print &#39;standard deviation:&#39;, data.std()</pre>
</div>

<div class="out">
<pre>maximum inflammation: 20.0
minimum inflammation: 0.0
standard deviation: 4.61383319712
</pre>
</div>


<div class="">
<p>When analyzing data, though, we often want to look at partial statistics, such as the maximum value per patient or the average value per day. One way to do this is to select the data we want to create a new temporary array, then ask it to do the calculation:</p>
</div>


<div class="in">
<pre>patient_0 = data[0, :] # 0 on the first axis, everything on the second
print &#39;maximum inflammation for patient 0:&#39;, patient_0.max()</pre>
</div>

<div class="out">
<pre>maximum inflammation for patient 0: 18.0
</pre>
</div>


<div class="">
<p>We don't actually need to store the row in a variable of its own. Instead, we can combine the selection and the method call:</p>
</div>


<div class="in">
<pre>print &#39;maximum inflammation for patient 2:&#39;, data[2, :].max()</pre>
</div>

<div class="out">
<pre>maximum inflammation for patient 2: 19.0
</pre>
</div>

<div class="">
<p>Note that this is effectively the same mechanism as shell pipelines. You can call methods on top of methods so long as those methods return valid Python objects, without needing to assign these intermediary objects to variables.</p>
</div>


<div class="">
<p>What if we need the maximum inflammation for <em>all</em> patients, or the average for each day? As the diagram below shows, we want to perform the operation across an axis:</p>
</div>


<div class="">
<p><img src="img/python-operations-across-axes.svg" alt="Operations Across Axes" /></p>
</div>


<div class="">
<p>To support this, most array methods allow us to specify the axis we want to work on. If we ask for the average across axis 0, we get:</p>
</div>


<div class="in">
<pre>print data.mean(axis=0)</pre>
</div>

<div class="out">
<pre>[  0.           0.45         1.11666667   1.75         2.43333333   3.15
   3.8          3.88333333   5.23333333   5.51666667   5.95         5.9
   8.35         7.73333333   8.36666667   9.5          9.58333333
  10.63333333  11.56666667  12.35        13.25        11.96666667
  11.03333333  10.16666667  10.           8.66666667   9.15         7.25
   7.33333333   6.58333333   6.06666667   5.95         5.11666667   3.6
   3.3          3.56666667   2.48333333   1.5          1.13333333
   0.56666667]
</pre>
</div>


<div class="">
<p>As a quick check, we can ask this array what its shape is:</p>
</div>


<div class="in">
<pre>print data.mean(axis=0).shape</pre>
</div>

<div class="out">
<pre>(40,)
</pre>
</div>


<div class="">
<p>The expression <code>(40,)</code> tells us we have an N×1 vector, so this is the average inflammation per day for all patients. If we average across axis 1, we get:</p>
</div>


<div class="in">
<pre>print data.mean(axis=1)</pre>
</div>

<div class="out">
<pre>[ 5.45   5.425  6.1    5.9    5.55   6.225  5.975  6.65   6.625  6.525
  6.775  5.8    6.225  5.75   5.225  6.3    6.55   5.7    5.85   6.55
  5.775  5.825  6.175  6.1    5.8    6.425  6.05   6.025  6.175  6.55
  6.175  6.35   6.725  6.125  7.075  5.725  5.925  6.15   6.075  5.75
  5.975  5.725  6.3    5.9    6.75   5.925  7.225  6.15   5.95   6.275  5.7
  6.1    6.825  5.975  6.725  5.7    6.25   6.4    7.05   5.9  ]
</pre>
</div>


<div class="">
<p>which is the average inflammation per patient across all days.</p>
</div>


<div class="challenges">
<h4 id="challenges">Challenges</h4>
<p>As we previously saw, we can take slices of a character string. For example:</p>
</div>


<div class="in">
<pre>element = &#39;oxygen&#39;
print &#39;first three characters:&#39;, element[0:3]
print &#39;last three characters:&#39;, element[3:6]</pre>
</div>

<div class="out">
<pre>first three characters: oxy
last three characters: gen
</pre>
</div>


<div class="challenges">
<ol style="list-style-type: decimal">
<li><p>What is the value of <code>element[:4]</code>? What about <code>element[4:]</code>? Or <code>element[:]</code>?</p></li>
<li><p>What is <code>element[-1]</code>? What is <code>element[-2]</code>? Given those answers, explain what <code>element[1:-1]</code> does.</p></li>
<li><p>The expression <code>element[3:3]</code> produces an <a href="../../gloss.html#empty-string">empty string</a>, i.e., a string that contains no characters. If <code>data</code> holds our array of patient data, what does <code>data[3:3, 4:4]</code> produce? What about <code>data[3:3, :]</code>?</p></li>
</ol>
</div>

### Plotting


<div class="">
<p>The mathematician Richard Hamming once said, &quot;The purpose of computing is insight, not numbers,&quot; and the best way to develop insight is often to visualize data. Visualization deserves an entire lecture (or course) of its own, but we can explore a few features of Python's <code>matplotlib</code> here. First, let's tell the IPython Notebook that we want our plots displayed inline, rather than in a separate viewing window:</p>
</div>


<div class="in">
<pre>%matplotlib inline</pre>
</div>


<div class="">
<p>The <code>%</code> at the start of the line signals that this is a command for the notebook, rather than a statement in Python. Next, we will import the <code>pyplot</code> module from <code>matplotlib</code> and use two of its functions to create and display a heat map of our data:</p>
</div>


<div class="in">
<pre>from matplotlib import pyplot
pyplot.imshow(data)
pyplot.show()</pre>
</div>

<div class="out">
<pre>
<img src="../../novice/python/01-numpy_files/novice/python/01-numpy_74_0.png">
</pre>
</div>


<div class="">
<p>Blue regions in this heat map are low values, while red shows high values. As we can see, inflammation rises and falls over a 40-day period. Let's take a look at the average inflammation over time:</p>
</div>


<div class="in">
<pre>ave_inflammation = data.mean(axis=0)
pyplot.plot(ave_inflammation)
pyplot.show()</pre>
</div>

<div class="out">
<pre>
<img src="../../novice/python/01-numpy_files/novice/python/01-numpy_76_0.png">
</pre>
</div>


<div class="">
<p>Here, we have put the average per day across all patients in the variable <code>ave_inflammation</code>, then asked <code>pyplot</code> to create and display a line graph of those values. The result is roughly a linear rise and fall, which is suspicious: based on other studies, we expect a sharper rise and slower fall. Let's have a look at two other statistics:</p>
</div>


<div class="in">
<pre>print &#39;maximum inflammation per day&#39;
pyplot.plot(data.max(axis=0))
pyplot.show()

print &#39;minimum inflammation per day&#39;
pyplot.plot(data.min(axis=0))
pyplot.show()</pre>
</div>

<div class="out">
<pre>maximum inflammation per day

<img src="../../novice/python/01-numpy_files/novice/python/01-numpy_78_1.png">
minimum inflammation per day

<img src="../../novice/python/01-numpy_files/novice/python/01-numpy_78_3.png">
</pre>
</div>


<div class="">
<p>The maximum value rises and falls perfectly smoothly, while the minimum seems to be a step function. Neither result seems particularly likely, so either there's a mistake in our calculations or something is wrong with our data.</p>
</div>


<div class="challenges">
<h4 id="challenges">Challenges</h4>
<ol style="list-style-type: decimal">
<li><p>Why do all of our plots stop just short of the upper end of our graph? </p></li>
<li><p>Create a plot showing the standard deviation of the inflammation data for each day across all patients. (Hint: the function standard deviation function is std() )</p></li>
</ol>
</div>

### Wrapping Up


<div class="">
<p>It's very common to create an <a href="../../gloss.html#alias-library">alias</a> for a library when importing it in order to reduce the amount of typing we have to do. Here are our three plots side by side using aliases for <code>numpy</code> and <code>pyplot</code>:</p>
</div>


<div class="in">
<pre>import numpy as np
from matplotlib import pyplot as plt

data = np.loadtxt(fname=&#39;inflammation-01.csv&#39;, delimiter=&#39;,&#39;)

plt.figure(figsize=(10.0, 3.0))

plt.subplot(1, 3, 1)
plt.ylabel(&#39;average&#39;)
plt.plot(data.mean(0))

plt.subplot(1, 3, 2)
plt.ylabel(&#39;max&#39;)
plt.plot(data.max(0))

plt.subplot(1, 3, 3)
plt.ylabel(&#39;min&#39;)
plt.plot(data.min(0))

plt.tight_layout()
plt.show()</pre>
</div>

<div class="out">
<pre>
<img src="../../novice/python/01-numpy_files/novice/python/01-numpy_83_0.png">
</pre>
</div>


<div class="">
<p>The first two lines re-load our libraries as <code>np</code> and <code>plt</code>, which are the aliases most Python programmers use. The call to <code>loadtxt</code> reads our data, and the rest of the program tells the plotting library how large we want the figure to be, that we're creating three sub-plots, what to draw for each one, and that we want a tight layout. (Perversely, if we leave out that call to <code>plt.tight_layout()</code>, the graphs will actually be squeezed together more closely.)</p>
</div>


<div class="challenges">
<h4 id="challenges">Challenges</h4>
<ol style="list-style-type: decimal">
<li>Modify the program to display the three plots on top of one another instead of side by side. (Hint: subplot(col, row, index starts from 1) )</li>
</ol>
</div>


<div class="keypoints">
<h4 id="key-points">Key Points</h4>
<ul>
<li>Import a library into a program using <code>import libraryname</code>.</li>
<li>Use the <code>numpy</code> library to work with arrays in Python.</li>
<li>Use <code>variable = value</code> to assign a value to a variable in order to record it in memory.</li>
<li>Variables are created on demand whenever a value is assigned to them.</li>
<li>Use <code>print something</code> to display the value of <code>something</code>.</li>
<li>The expression <code>array.shape</code> gives the shape of an array.</li>
<li>Use <code>array[x, y]</code> to select a single element from an array.</li>
<li>Array indices start at 0, not 1.</li>
<li>Use <code>low:high</code> to specify a slice that includes the indices from <code>low</code> to <code>high-1</code>.</li>
<li>All the indexing and slicing that works on arrays also works on strings.</li>
<li>Use <code># some kind of explanation</code> to add comments to programs.</li>
<li>Use <code>array.mean()</code>, <code>array.max()</code>, and <code>array.min()</code> to calculate simple statistics.</li>
<li>Use <code>array.mean(axis=0)</code> or <code>array.mean(axis=1)</code> to calculate statistics across the specified axis.</li>
<li>Use the <code>pyplot</code> library from <code>matplotlib</code> for creating simple visualizations.</li>
</ul>
</div>


<div class="">
<h4 id="next-steps">Next Steps</h4>
<p>Our work so far has convinced us that something's wrong with our first data file. We would like to check the other 11 the same way, but typing in the same commands repeatedly is tedious and error-prone. Since computers don't get bored (that we know of), we should create a way to do a complete analysis with a single command, and then figure out how to repeat that step once for each file. These operations are the subjects of the next two lessons.</p>
</div>
