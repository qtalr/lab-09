# Lab 09: Text classification

## Preparation

- Read/ annotate: [Recipe \#9](https://qtalr.com/resources/recipes/recipe-09/). You can refer back to this document to help you at any point during this lab activity.

## Objectives

- Review skills and knowledge on performing diagnostic checks on data
- Learn to frame research questions to perform predictive analysis
- Apply skills and knowledge to identify, inspect, interrogate, and interpret the results of predictive analysis

## Instructions

### Getting started

In this lab, you will be using Git and Github to fork, clone, commit, and push changes to a repository. The repository you will select to use as the repository to fork to your own Github account can be one of the following:

- [Lab 09 repository](https://github.com/qtalr/lab-09)
- [Minimal template](https://github.com/qtalr/project)
- [Web-based project template](https://github.com/qtalr/project_web)
- Other (consult your instructor)

If you are starting with a new repository, fork and clone the repository you selected to your local machine. Then orient yourself to the repository by opening the README file and reviewing the template configuration.

If you are using an existing reproducible research project repository, open that project on your local machine, and `pull` the latest changes from the remote repository to ensure that your local and remote repositories are in sync.

Open a Quarto document in the process directory and name it accordingly (e.g., `4_analysis.qmd`, `analysis.qmd`, *etc.*).

The data you select to explore should be in a format conducive for a text classification task. The options include the following:

- Europarl Corpus of Native, Non-native, and Translated Texts (see [below for details](#europarl-corpus-of-native-non-native-and-translated-texts))
- The dataset you transformed in Lab 07
- Other (consult your instructor)

### Predictive analysis

In your analysis process file,

1. add a section which provides a brief description of the dataset you will be using and the task setup and aims. Include:

  - the name and/ or source of the data
  - the nature of the data
  - the outcome variable and the predictor variable(s)

2. add a section which provides a description of the potential features you will use and engineer in this task. Include:

  - a description of linguistic and non-linguistic features you will use and why you think they will be useful in this task
  - a description of the process you aim to use to engineer these features

3. add a section which provides a description of the modeling process you will be using to perform the task. Include:

  - a description of the high-level modeling process you will be using to perform the task (e.g. classification, regression, *etc.*)
  - for each modeling process, a description of the specific modeling process you will be using to perform the task (e.g. classification using a random forest model, *etc.*)
  - a description of the process you will use to evaluate the performance of the model(s)

4. Implement and make sure to organize your analysis process in a way that is reproducible. This means that you should be able to run the code in your process file and reproduce the process (use `set.seed()` for any sampling process, for example). Use the `data/analysis` (or similar) directory to store any derived datasets used in the analysis.

5. Make sure that your code is well documented with code comments and that you have included prose to describe the process of analyzing the dataset.

6. Include a section to describe the results of your analysis. This will contain the results of the model evaluation process, and may include an exploration of feature importance.

7. Confirm that your code runs without errors and that the code, visualizations, and/ or tables are displayed as expected.

8. Finally, commit and push your changes to your Github repository. *Make sure to include files or directories that you do not have permission to share in your `.gitignore` file.*

### Assessing your progress

1. In your repository on Github, open an issue to provide feedback on your experience with this lab (Click on the 'Issues' tab and then click the 'New issue' button). Title the issue "Lab 09 feedback" and provide your feedback in the body of the issue.

Some questions to consider:

  - What did you learn?
  - What did you find most/ least challenging?
  - What resources did you consult?
    - Instructor? R or Quarto documentation, Websites (provide links)?
  - What more would you like to know about predictive analysis?
    - Find potential resources you might consult to continue your learning. Provide links and a brief description of the resource.

## Submission for feedback

- Provide a link to your GitHub repository

## Europarl Corpus of Native, Non-native, and Translated Texts

```r
library(qtalrkit)

# Acquire ---------------

get_compressed_data(url = "https://github.com/nlp-unibuc/nlp-unibuc-website/releases/download/v1.0/ENNTT.tar.gz", target_dir = "data/original/")

# Curate ---------------

df <- curate_enntt_data("data/original/ENNTT") # This will take a while!
```

This will provide you with a curated dataset with the following variables:

| Variable    | Name             | Description                                                    |
| ----------- | ---------------- | -------------------------------------------------------------- |
| session_id  | Session ID       | The session ID of the speech                                   |
| speaker_id  | Speaker ID       | The speaker ID of the speech                                   |
| state       | State            | The political state of the speaker                             |
| session_seq | Session sequence | The sequence of the speech in the session                      |
| text        | Text             | The text of the speech                                         |
| type        | Type             | The type of language (i.e. Native, Non-Native, or Translation) |


You will subsequently transform this dataset in accordance with the task you select to perform.

```r
# Transform --------------

df |>
  ...

```

Note on a few standard transformations:

- Subset the data to the variables you will use in the analysis
- Subset the data to the observations you will use in the analysis
- Convert the outcome variable to a factor

One other, non-standard transformation you may want to consider concerns the number of observations in each class. There is a significant imbalance in the number of observations in each `type`:

| type         |      n | percent |
| :----------- | -----: | :------ |
| natives      | 116,341 | 13.15%  |
| nonnatives   |  29,734 | 3.36%   |
| translations | 738,597 | 83.49%  |

In the case of classification, this imbalance may lead to a model that is biased towards the majority class. You may want to consider balancing the number of observations in each class in the building of your recipe with the `step_downsample()` function from the `themis` package.

```r
library(themis)

recipe(
  type ~ .,
  data = df
) |>
step_downsample(type)
```
