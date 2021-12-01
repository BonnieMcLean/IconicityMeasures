# General overview

Icotools is a [python package](https://pypi.org/project/icotools/). To install it, use `pip install icotools`.

Icotools comes with three functions:

- `icotools.rater('stimuli.csv','controlfile.csv')`
- `icotools.guesser('stimuli.csv','controlfile.csv')`
- `icotools.foiler('stimuli.csv','letters.csv')` 

The **rater** function makes rating experiments, where participants are presented with stimuli and their meanings, and asked to rate on a 7-point scale whether they think there is a resemblance between the stimulus and its meaning, from 0 'No resemblance' to 6 'Strong resemblance'. A demo is available [here](https://honestcookingblog.com/ratings/experiment1.html#). 

The **guesser** function makes guessing experiments, where participants are given a meaning and have to match the meaning to the correct stimulus, in a two alternative forced-choice paradigm. A demo is available [here](https://honestcookingblog.com/experiments/experiment1.html#). You can either:

(a) make guessing experiments where the foils for each item are specified, or   
(b) make guessing experiments where the foils for each item are randomly chosen from all the items available, with the option to specify particular item-foil pairings which should *not* be used.

If you don't specify your own foils, option (b) will be chosen automatically.

## Why specify foils?

We have found that words are usually guessed more accurately when paired with foils that are as different-sounding to them as possible, compared to when paired with random foils. Thus, using opposite foils can improve the sensitivity of your guessing experiments to iconicity, particularly in cases where the items you are testing are expected to exhibit only weak iconicity, when you do not have so many guesses per item, or when you do not have so many items in your study. We also think it is a more principled way to test for iconicity, since if a word does sound appropriate for a meaning, then a word that sounds very different to it should be *inappropriate* for that meaning. 

The function **foiler** provides a list of opposite-sounding words for each item in your experiment. At the moment, the **foiler** function is only supported for items that are *spoken language words or sounds* (able to be transcribed in IPA). If the items in your experiment are hand signs or gestures, we recommend using random foils. As items in the visual modality are often higher in iconicity anyway, using opposite foils would be unlikely to greatly improve the sensitivity of the measure. However, if you have some way of choosing maximally contrastive signs or gestures to pair with your items, you are free to specify this in the 'foils' column of your stimuli file (See [here](#Format-for-stimuli-file))

## Available stimuli types

At the moment, the following types of stimuli are supported:

- mp3 or wav 
- mp4

We are planning to add support for images soon. If you would like support for more types of stimuli, please request it in the 'Issues' section.

## How do the guessing and rating functions work? 

Each function takes a list of stimuli (stimuli.csv) and a control file (control.csv) as inputs, and produces a folder of html experiments to collect measures of iconicity on those items. For each experiment, three files are produced--a html file, a php file, and a csv file. Once you upload all these files to your server, and share the link to the html experiments with participants, as participants complete your study, their answers (as well as their reaction times for each item, and a record of which translation/foil was presented to them for each item) will be written to the csv file on your server (by the php). The functions also produce a summary csv file with all the details of the experiments (i.e. the trials in each experiment, as well as the translations and, where applicable, foils used).

There are examples in the 'examples' folder. The file `processing.Rmd` contains R scripts for combining the raw result files from the experiments into one long tidy dataset. 

# Format for control file

It should be a csv file with two columns, the first column contains variable names and the second column contains their values.

|Col1             |Col2                                                                                                             |
|-----------------|-----------------------------------------------------------------------------------------------------------------|
|media_source     |link to location of media files                                                                                  |
|media_type       |media format (either 'mp3','wav', or 'mp4')                                                                      |                                            
|mp4_type         |(if using mp4 format) specify either 'gesture' or 'sign' (for the wording in the questions)                      |
|muted_vids       |(optional) either 'y' or 'n' if you want to mute the audio in the videos (default is 'n')                        |
|language         |(optional) language of the stimuli (if you want to inform participants of that)                                  |
|headphone_check  |do you want to include a headphone check at the start of the experiment--either 'y' or 'n'                       |                                           
|instructions_html|either 'default' or provide filename to your own html instructions                                               |       
|exitques_html    |either 'default' or provide filename to your own html exit questions                                             |  
|exitques_labels  |(if not using default exit questions) a comma separated list of labels for your exit questions (for the csv file)|
|submit_html      |either 'default' or provide filename to your own html submit message (e.g. if you need to redirect participants) |
|words_per_exp    |roughly how many words would you like to test per experiment (excluding practice and control items)              |                                         

The order of the variables doesn't matter.
See examples in the example folder.

# Format for stimuli file
It should be a csv file. The first three columns are required, but the last three are optional. Having extra columns (with stuff not relevant to the experiments) will not matter.

| form                                                         | item                                                         | meaning                                   | foils                                                        | no_foils                                                     | iconic                                                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ----------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| the form to be tested--this should correspond to the name of your media files, e.g. if the form is honnori the media file should be called honnori.mp3 or honnori.mp4 etc. (depending on what you put in the control file) | either 'practice' (if it's a practice item), 'control' (if it's a control item) or 'trial' (if it's one of the items you're testing)--see explanation below table | one or more translations, separated by \| | the names of items you want to use as foils. These should match their media file names (as for the form column), and be separated by \| | the names of items you don't want to use as foils. These should match their media file names (as for the form column), and be separated by \| | if you want to have a roughly equal number of possibly iconic vs possibly not iconic words in each experiment, indicate your hypothesis about iconicity 'y' or 'n' here |

Practice and control items are included in every experiment (but you only have to list them in the stimuli file once). The practice items are always the first two items in every experiment, while the order of the trial items and the control items is randomised between participants. The choice of foil and the choice of translation (in the meaning column) is also randomly varied between participants (for the meanings/foils listed). If you don't list any foils, then the foil will be randomly chosen from the other trials. We recommend providing multiple translations in the meaning column for each trial where possible, so that the choice of translation does not too strongly influence the results. Try to choose translations that are similar in meaning, but different in sound (see the paper for more discussion). 

The meanings will also all be presented in CAPITALS during the experiment (regardless of how you write them in the stimuli file).

See examples in the example folder.

# Instructions for using the foiler() function

If you have geminate consonants, please represent them using doubled letters if you want them to be converted to single letters. If you have long vowels you can use doubled letters or the length symbol in IPA; both will work. 

By default, the formula for calculating the phonological distance between two segments—or between a segment and “silence”, i.e. insertion or deletion—is the one used in the Sublexical Learner [Allen2014]. When comparing two segments, the distance between them is equal to the sum of the distances between each of their feature values: the distance between two feature values that are identical is 0, while the distance between two opposing values (+/- or -/+) is 1, and the distance between two feature values in the case that just one of them is 0 (unspecified) is set to by default to 0.25.

# Demo

Below is an example of how to use the functions in icotools from python:

![](terminal.PNG)

You can find the input files in the folder 'examples/foiler_examples'.

# Default instructions

## Guesses - mp3/wav stimuli

'Japanese' will be replaced with whatever language you specify in the control file. If you don't specify a language, the instructions will just refer to 'an unknown language'.

>  <p>We are interested in how well people can guess words in foreign languages. In this experiment, you will be asked to match the English translation with the corresponding word in Japanese, guessing from a choice of two Japanese words.</p>
>  <p><strong>Criteria for participation</strong></p>
>  <p>Because your answers should be guesses, it is very important for our experiment that you do NOT speak or understand Japanese. Also, since the experiment requires you to listen to the words, you will need to complete it in a quiet place using headphones. We will check at the beginning of the experiment that you are using headphones, so please make sure to use them as participants who do not use headphones will not be able to complete the experiment.</p> 
>  <p>Participation is completely voluntary, anonymous and confidential. If you meet the above criteria and agree to participate, please click 'Participate'.</p>

## Guesses - mp4 stimuli

Below are the instructions for signs, in this case from JSL. JSL will be replaced with whatever language you specify.

> We are interested in how well people can guess the meanings of signs in JSL. In this experiment, you will be asked to match the English translation with the corresponding sign in JSL, guessing from a choice of two JSL signs.  
> **Criteria for participation**  
> For your judgments to be unbiased, it is very important that you do NOT know any JSL. If you do know JSL, we kindly ask that you do not participate in this study.  
> Participation is completely voluntary, anonymous and confidential. If you meet the above criteria and agree to participate, please click 'Participate'.

Below are the instructions for gestures. If you specify a language then it will replace 'foreign' with the language you specify.

> We are interested in how well people can guess the meanings of foreign gestures. In this experiment, you will be asked to match the English translation with the corresponding gesture, guessing from between a choice of two gestures.
> Participation is completely voluntary, anonymous and confidential. If you meet the above criteria and agree to participate, please click 'Participate'.

## Ratings - mp3/wav stimuli

'Korean' will be replaced with whatever language you specify.

> Some words seem to 'fit' their meanings. For example, consider the English words *wiggle*, *jiggle*, and *wriggle*.  
> We have an intuitive sense of the meanings of these words, because there is a resemblance between the words and their meanings.  
> Even people who do not speak any English can get a sense of the meaning of these words.  
> Words like walk and run on the other hand are not so intuitive; people who do not know any English would not be able to guess what these words mean.  
> In this task, you will listen to some Korean words, and we will tell you their meanings. You will then be asked to judge whether there is a resemblance between the word and its meaning.  
> **Criteria for participation**  
> For your judgments to be unbiased, it is very important that you do NOT know any Korean. If you do know Korean, we kindly ask that you do not participate in this study.  
> Since the task requires you to listen to the words, you will need to complete it in a quiet place using headphones. We will check at the beginning of the task that you are using headphones, so please make sure to use them as participants who do not use headphones will not be able to complete the task.  
> Participation is completely voluntary, anonymous and confidential. If you meet the above criteria and agree to participate, please click 'Participate'.

## Ratings - mp4 stimuli

For signs:

> In this task, you will be presented with some JSL signs, and we will tell you their meanings. Rate how well you think the sign depicts its meaning.  
> **Criteria for participation**  
> For your judgments to be unbiased, it is very important that you do NOT know any JSL. If you do know JSL, we kindly ask that you do not participate in this study.  
> Participation is completely voluntary, anonymous and confidential. If you meet the above criteria and agree to participate, please click 'Participate'.

For gestures:

> In this task, you will be presented with some foreign gestures, and we will tell you their meanings. Rate how well you think the gesture depicts its meaning.  
> Participation is completely voluntary, anonymous and confidential. If you meet the above criteria and agree to participate, please click 'Participate'.
