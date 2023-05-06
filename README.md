# HLAN replication

## CS 598 - Deep learning for health care - paper replication
## Replication of "Explainable Automated Coding of Clinical Notes using Hierarchical Label-wise Attention Networks and Label Embedding Initialisation" by Hang et al

### Introduction
This is a repository with the code of my independent replication of a medical paper using deep learning. It provides a to a maximum degree possible a standalone code to prepare data and run end-to-end the model.

### Data preparation
#### Getting data access
The model uses MIMIC-III dataset, which is not in open domain. One can obtain access via physionet.org, after completed data privacy and protection trainings.
The following datasets are required:
- D_ICD_DIAGNOSES.csv
- D_ICD_PROCEDURES.csv
- NOTEEVENTS.csv
- DIAGNOSES_ICD.csv
- PROCEDURES_ICD.csv

#### Preparing raw dataset
The replicated paper refers to https://github.com/jamesmullenbach/caml-mimic for pre-processing. 

mimicdata
|   D_ICD_DIAGNOSES.csv
|   D_ICD_PROCEDURES.csv
|   ICD9_descriptions (already in repo)
└───mimic3/
|   |   NOTEEVENTS.csv
|   |   DIAGNOSES_ICD.csv
|   |   PROCEDURES_ICD.csv
|   |   *_hadm_ids.csv (already in repo)



It enables uses to explore numerous interesting attributes.

Explanatory features using MySQL as a backend are as follows:
- For a keyword (i.e. academic research topic) provided by a user, explore the average number of citations for 5 "best" (i.e. most cited) papers in recent few years.
- For a selected year, visualize a relationship between number of authors and number of citations for the most successulf papers, for most relevant topics.
- Match academic journal name and find and visualize the most impactful articles, aling with number of citations, publishing year and citation relevance.
- Find "Publishing leaders" - i.e. for a topic given by the user, find universities with the highest number of members producing very impactful articles, i.e. whose citations exceed a threshold dynamically selectably by user.

MySQL backend also supports the following CRUD operations:
- updating value of certain attributes about faculty members,
- insertion of new publications.

For these insertions, the database handles referential integrity - for example adding a new publication with a specific keyword will be reflected in publication_keyword mapping table and authorship will be reflected in faculty_publication table.

Neo4J is used for the following analysis:
- provide a short list of faculty members by matching their names,
- then for the user selected by mouse click, find a shortest path between the member and another member with an interest in a certain topic, where the edge in the path is defined as a common interest between 2 faculty members.

MongoDB provides a backend to select the most common topics of publications published in a given year.

### Demo
Media Space UIUC:
https://mediaspace.illinois.edu/media/t/1_ozkznnxz

 ### Installation.

1. Install MySQL, MongoDB and Neo4J
2. Create the adacemicworld database for each of the DBS, following instructions in MP3.
3. Save in params_yaml.yaml passwords and username for the 3 DBS. The variables storing the credentials are:
neo4j_username: XYZ
neo4j_password: XYZ
mysql_username: XYZ
mysql_password: XYZ
mongo_username: XYZ
mongo_password: XYZ

Easiest thing is to adjust the file I shared.

4. For enhanced performance, create the following indices:
CREATE INDEX NAME_INDEX ON FACULTY(NAME);
CREATE INDEX VENUE_INDEX ON PUBLICATION(VENUE);
ALTER TABLE publication_keyword MODIFY COLUMN SCORE INT DEFAULT 0.3;
ALTER TABLE PUBLICATION MODIFY COLUMN NUM_CITATIONS INT NOT NULL;

5. Create the following SQL views:
publication_faculty:
`create view publication_faculty as (
select distinct f.name, p.id pid, f.id fid, p.year, p.venue, p.title, p.num_citations, f.university_id
from faculty f 
inner join faculty_publication fp on f.id = fp.faculty_id 
inner join publication p on fp.publication_id = p.id);`

6. `cd` into the copied from GitHub and run `app.py`. The folder include a csv file with some keywords, which are used as a list of options in some dropdowns.

7. Copy into the browser the address on which Dash is running, e.g. http://127.0.0.1:8050/

### Usage
Follow the description in the dashboard and explore topics of interest.

The database queries should be working correctly, but some of the more complex Dash GUI features are not covering all corner cases. Hence if something does not work as expected, I recommend refreshing the browser. 

### Design
The applications is split to 4 tabs:
- "SQL" - for exploration using SQL backend,
- "SQL - edits" - for updates and edits of the SQL DB (updating faculty table info and inserting info into publication table)
- Neo4j - for exploring shortest path,
- MongoDB - for exploring most common publication topics.

### Implementation
The front end is build in Dash, python framework for interactive browser-based applications. app.py is the main file.

Backend is in python, using the following packages with self-explanatory names to connect to the respective databases:
- mysql
- pymongo
- neo4j.

For each of those, there is "_utils.py" file, wuth some helper functions.
### Database Techniques
I use the following techniques:
- ***Indexing*** - I have created 2 new indices to enhance performance of search of "venue" and for some of the joins:
`CREATE INDEX NAME_INDEX ON FACULTY(NAME);
CREATE INDEX VENUE_INDEX ON PUBLICATION(VENUE);`
- ***Constraints*** - Non-null constraint has been added to the number of citations. `ALTER TABLE PUBLICATION MODIFY COLUMN NUM_CITATIONS INT NOT NULL;` In the end, it turned out to be redundant as GUI requires filling all required information.
- ***View*** - View `publication_faculty` is used to facilitate some queries and visualizations (top 2 SQL charts). It is defined above.


### Extra-Credit Capabilities: 
Not sure if it qualifies, but I think some of the exploratory SQL features are rather interesting and I think I selected reasonable visualizations tools. E.g. from the scatterplot on "Citation and authorship patterns for top papers in key areas", one can see som patterns, such as mostly medical related research having many co-authors. I also resolved in a intuitive but simple way how to visualize shortest path "graph".

### Contribution
I worked on the app alone. I spent estimated 35-40 hours on it - mostly on Dash (probably 80% of time and effort) which was new to me.
