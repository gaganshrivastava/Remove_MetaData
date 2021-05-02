# Remove_MetaData
 Remove the Meta data from PDF file

Sanitize Metadata

Overview
This lambda function removes the Metadata from the word document (docx) or PDF Document. The Metadata are the properties like (Author, Tittle, Revision, created by, etc..). To remove the metadata I have created two separate functions, one for word file and another one for the PDF files. 

Below is the list of different metadata properties in the PDF file. From which except Advance properties sections, I am able to remove all.


We Can see the metadata from PDF file using below steps:
Open the file in Adobe Acrobat Reader.
Right click on the document. Choose the Document Properties (CTRL + D) OR at the top click on File, then choose properties.

Check the various properties.






Below is the List of different properties in Word File. I am able to remove the items which are in the green background.


Steps to See and remove the metadata in word docx manually:
Open file in Microsoft word.
Go to File > Info > Inspect Document(check for issues) > click on Inspect 

TO remove the metaData manually click on removeAll. Then Save the file.




Sanitize Metadata from PDFs
I used PDFTron to remove the metadata from the PDFs file. Lambda function take the Input in JSON format, validate the input JSON then download the file from S3 bucket into local repository (‘/tmp/’) folder, remove the metaData properties from the file and upload it on S3 bucket(‘sanitize bucket’). 

Implementation
Coding Language  : Python 3.7
Library/Tools used : VSCode, AWS Cloud9, PDFTron, AWS S3, AWS Lambda

PDFTron is an open source library which we can use to perform almost all the operations on a PDF file. It supports multiple languages like Node.js, Java, Python etc. I have used this library in merging docx/ pdf files into a single PDF.
In python it supports upto version <3.8.

Create a Lambda function, add the required roles to it from the Permission setting inside Configuration. Role likeAmazonS3FullAccess. 

After creating the lambda function setup the code in local.
Command to install the library: 
> python -m pip install boto3 -t .
> python -m pip install PDFNetPython3 -t .

To upload the code on Lambda, we have to zip all the libraries and code together. 
Command to zip the code and library into the current directory: 
> zip -r lambda_function.zip .
(Period means all)

To run and test the lambda function, cross check the runtime setting of lambda function.
In Handler the first parameter should be the file_name of the code file. Like for example my code file name is “lambda_function.py”, so lambda_function should come first. Then function name which invoked our lambda function and which takes the event and context as an input.


Input
{
  "files": [
    {
      "displayName": "CS351_HW1.pdf",
      "key": "a0aa0f2c-da69-4420-b5ea-bcd06b93ffb1/1617646540564/a0aa0f2c-da69-4420-b5ea-bcd06b93ffb116176465405641.vnd.openxmlformats-officedocument.wordprocessingml.document",
      "location": "https://unsanitized-bucket.s3.amazonaws.com//bb41fc75-4e28-4c9b-862e-fe79393aa8a2/1617215163866/bb41fc75-4e28-4c9b-862e-fe79393aa8a216172151638661.pdf"
    }
  ],
  "selectedOptions": {
    "stripMetaData": 1,
    "textExtract": 0,
    "virusScan": 0,
    "imageRecognition": 0,
    "convertDocument": 1,
    "documentClassification": 0,
    "redactPII": 0
  },
  "userID": "userID",
  "submitTime": "epsilonTime"
}



Output
{
  "statusCode": 200,
  "files": [
    {
      "displayName": "CS351_HW1.pdf",
      "key": "a0aa0f2c-da69-4420-b5ea-bcd06b93ffb1/1617646540564/a0aa0f2c-da69-4420-b5ea-bcd06b93ffb116176465405641.vnd.openxmlformats-officedocument.wordprocessingml.document",
      "location": "https://unsanitized-bucket.s3.amazonaws.com//bb41fc75-4e28-4c9b-862e-fe79393aa8a2/1617215163866/bb41fc75-4e28-4c9b-862e-fe79393aa8a216172151638661.pdf"
    }
  ],
  "selectedOptions": {
    "stripMetaData": 0,
    "textExtract": 0,
    "virusScan": 0,
    "imageRecognition": 0,
    "convertDocument": 0,
    "documentClassification": 0,
    "redactPII": 0
  }
}


Issues/Error Occurred While Developments
 
 After exploring a couple of libraries I got the PDFTron which is suitable for all use cases.The libraries which didn’t work because of errors are 

Pikepdf( "Not able to find the metadata properties in a tree structure path."), 
Basic Properties can be easily removed but not get enough documentation for advanced properties.

Reference :
pip install pikepdf
pdf = pikepdf.open('NJIT Intern Packet Spring 2021_Gagan.pdf')
print(pdf)
meta = pdf.open_metadata()
print(meta )

 https://pikepdf.readthedocs.io/en/latest/topics/metadata.html
https://pikepdf.readthedocs.io/en/latest/topics/metadata.html#accessmetadata


Pdfrw::
This seems to work but have to set all th properties individually to null.
And have to find the exact path for all the properties. Which seems to be difficult task.

PYPDF2 : Use to extract  the metadata only, can’t remove from the file.
import PyPDF2 

f = open('NJIT Intern Packet Spring 2021_Gagan.pdf','rb')
pdf = PyPDF2.PdfFileReader(f)
print(pdf.getDocumentInfo())

'pdf-parse' :: node js 
In this we can extract the meta data but not able to remove it from the file.’


     
