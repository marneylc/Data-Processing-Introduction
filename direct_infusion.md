Signal processing with xcms

There is a full tutorial from the makers of XCMS here: http://www.bioconductor.org/packages/release/bioc/vignettes/xcms/inst/doc/xcmsPreprocess.pdf .  XCMS is an R package that enables the visualization and some intense processing of chromatographic and mass spectrometry data files. It also contains some fantastic tools for some basic processing that can be built into custom tools. It requires that the data be converted to an open format such as mzXML prior to attempting to load in the data.

Proteowizard needs to be installed and can be found here: http://proteowizard.sourceforge.net/downloads.shtml

First we are going to look at direct infusion orbitrap mass spectrometry data. If you have a couple .RAW Xcalibur files you can use those. Use proteowizard to convert them to mzXML without any additional peak picking or processing available with proteowizard. There are a couple of mzXML files available for download from my google drive account here: https://drive.google.com/folderview?id=0B06AQDbcyIg8Q2ZFYm1RNmhnMUE&usp=sharing

Okay, open up R or preferably RStudio. Now where did you put the mzXML files? Let's store that information in a variable. A variable in a computer language is very similar to its definition in algebra. It holds a value.

I put my files in a temp folder in my Downloads directory. If yours is different be sure to put the correct path in. We also want to set our current working directory to the folder as well.

``` R
path = "C:/Users/marneyl/Downloads/temp/"
setwd(path)
```

Now take a look at the contents of the folder from within R.

``` R
list.files(path)
```

list.files() and setwd() are called functions. Both setwd() and list.files() are built in functions that come with the installation of R. Functions take argument, in our case the variable path, and return values. There is some internal process that turns the path that you give the function list.files() into a list of file names in that folder. In the future you can write your own functions to take arguments, like data files, and do some stuff to them and spit out something informative and useful.

Another extremely useful function in R is the help() function, which pulls up the help file for any other function or object. Try it,

``` R
help(list.files)
```

or

``` R
?list.files
```

Now we are going to pass a little extra information in case there are other files in the folder. We will define the variable 'files' to be a list of filename that we can use to load in specific files, or later loop through to process a batch of files easily.

'''
file = list.files(path, pattern = '.mzXML')
'''

I think you can imagine what the pattern definition does. In code-speak pattern is an argument passed to the function list.files. Lots of functions have optional arguments that can be very useful. Be sure to read the documentation if you ever want a function to do something a little more, there could be a solution in the optional arguments. Find the options with ?list.files or help(list.files).

We are going to use the package xcms to read and parse the information in the mzXML files. So first we import the package using the library() fucntion. If you need to install it, you can find the source code at bioconductor with direction on how to install it for your OS.

'''R
library(xcms)
'''

The simplest thing to do is to plot the TIC from the mzXML file, which will help us decide later which time range to use to create an average spectrum to process later. First we have to create an xcmsRaw-object. An object is an entity in coding that contains both data and methods. So we are going to store the complete spectral data into an xcmsRaw-object, plus we can use the encoded methods in that object to easily plot the TIC. There are also a lot of other things we can do with xcms. You can read about them again with ?xcmsRaw-object

'''R
data <- xcmsRaw(files[1])
plotTIC(data)
'''

That's a pretty handy method for quickly seeing our infusion data. We can now pick our retentiontime window to get spectra from.

Now we are going to define a spectrum variable that will hold an averaged spectrum from the mzXML file. We are again going to use a method of the xcmsRaw object that we defined as the variable data. The method we will use is getSpec().

'''R
spectrum <- getSpec(data, rtrange=c(0,60), mzrange=c(200,1800))
'''

What does c(0,60) and c(200,1800) mean? How could you find out? Maybe try ?c

'''
If you want to take a peak at the spectrum variable and what it contains you can type in either

'''R
View(spectrum)
'''
or
'''R
spectrum
'''

And watch the numbers fly!

Let's look at it as we are used to, as a bar plot.

'''R
plot(spectrum, type='h', lwd=1)
'''

How could we zoom in?

Next we will do one of the simplest processing methods. Lets see what the difference between two samples is. First we will make another xcmsRaw-object for another file, define the variable spectrum2 to be the second spectrum, and then take the difference between the two samples.

'''
data2 <- xcmsRaw(files[2])
spectrum2 <- getSpec(data2, rtrange=c(0,60), mzrange=c(200,1800))
'''

Now we have a problem. Look at the two spectral data with View() and take a look at the output from the following code.

'''
min(spectrum[,1])
min(spectrum2[,1])

max(spectrum[,1])
min(spectrum2[,1])

diff <- spectrum[,2] - spectrum2[,2]
'''

What error message do you get? Why is that? How could we solve that?
See if you can figure out how to take the difference between these two spectral files.

Hint:
'''R
subset <- spectrum[1:20,]
'''

Don't be distracted by the NA values, that just means the mass spectrometer didn't detect any signal for those m/z values.

Think about how you would go about importing in MRM chromatographic data. Take a look at the xcms documentation online and see if you can figure it out. This is my lazy way of not writing a tutorial on this as of yet :)
