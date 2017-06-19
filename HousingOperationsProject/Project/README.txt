1. Install the necessary packages needed using the commands

npm install watson-developer-cloud --save
npm install solr --save
npm install jsonfile --save 

These commands will add the dependencies to the package.json file.

2. The first step is to create a cluster online on Bluemix
and get the cluster ID. A cluster is the base for all operations concerned with Retrieve and Rank service of
Watson. This is to be done by logging into the Bluemix
console, launching the Retrieve and Rank tool and creating a cluster. It will take a few minutes before the
cluster is created and is ready to use. The user then
needs to note the cluster id.

3. Once the documents have been prepared in a way that
can be understood by Watson's Document Conversion
service, the user can execute docconversion.js file. To
run docconversion.js, the user needs to set the input
parameters files_directory to the path of the folder that
contains all the source documents in .doc format and
json_directory to the path of the folder where the user
wishes to get the JSON files after conversion. For example,

files_directory = `./resources/'
json_directory = `./JSONFiles/'

The command to run document conversion in command prompt is

node docconversion

4. After invoking Document Conversion service and receiving the JSON files, the next step is to upload Solr
configuration. The Solr configuration is required to index the files and search answers to any query. The provided solrconfig.zip has schema.xml and solrconfig.xml
that define how to index and search files, the fields in
text, request handlers etc. Before running uploadsolrconfiguration.js, the user has to set the parameters in
inputconfigurations.js as

cluster_id = `scd2968656_0ff2_4fcd_948a_56c37e87ef1f'
config_name = `HousingSolrConfig'
config_zip_path = `solrconfig.zip'

where config_name is a user-defined name that is given
to the Solr configuration that was uploaded, and config_zip_path is the path to the solr configuration zip file.
After successful execution, the status will be 200.

5. The above step is followed by indexing the documents
using Solr that will help with searching for answers.
This requires setting the parameter collection_name to
a user defined collection_name. For example,

collection_name = 'HousingCollection'

A collection is where the JSON answer units from Document Conversion will be stored along with the training questions and answers. The file indexdocuments.js
needs to be executed with the following command

node indexdocuments

It will take a few minutes to create a collection and it
might show a warning asking the user to update the
schema. In that case, the user needs to click on the
'Update Schema' button to update the configurations.

6. Now that all the components for using Retrieve and
Rank are ready, the user needs to provide training questions for Watson, which is called the ground truth. The
ground truth is a comma separated values file in the format question, answer1, relevancefactor1, answer2, relevancefactor2.... The question covers all possible 
questions that a user could ask Watson, answer1 refers to a
matching answer in the document, and the relevance factor denotes how relevant the answer is to the question - the higher the number, more relevant is the 
answer. Watson requires the user to write a minimum of
250 questions. This ground truth needs to be uploaded
to Watson to train the ranker module. This is done
using the train.py file provided by IBM. The command
to train the ranker is

python train.py -u retrieve_and_rank_username:retrieve_and_rank_password -i ground_truth_file -c cluster_id -x collection_name -n "user_defined_ranker_name"

7. It takes a few minutes before the ranker is ready to accept questions. Once it is ready, the user has to note the
ranker id. This value is to be entered in the ranker id of inputconfigurations.js. The user then has to enter their query that needs to be searched in the documents
in the question parameter of inputconfigurations.js file. The user can then run askquestions.js file which will give the most relevant and ranked answers.