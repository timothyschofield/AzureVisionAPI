Call the Azure AI Vision 3.2 GA Read API

https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/how-to/call-read-api

17 October 2023
=================================================================
======= Call the Azure AI Vision 3.2 GA Read API =======
=================================================================
This guide assumes you have already create a Vision resource and obtained a key and endpoint URL. 
If you haven't, follow a quickstart to get started.

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> (DOWN HOLE)
==== Quickstart: Azure AI Vision v3.2 GA Read ====
how to install the OCR client library and use the Read API

https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/quickstarts-sdk/
	client-library?tabs=windows%2Cvisual-studio&pivots=programming-language-csharp

OCR (Read) editions
Select the Read edition that best fits your requirements.
We are given a choise at this point between
	OCR for images (version 4.0 preview)
and
	Document Intelligence read model

both of which say they do handwriting...so carry on with current page anyway.

About Azure AI Vision v3.2 GA Read

Looking for the most recent Azure AI Vision v3.2 GA Read? 
All future Read OCR enhancements are part of the two services listed previously. 
There are no further updates to the Azure AI Vision v3.2. 
For more information, see 
	Call the Azure AI Vision 3.2 GA Read API and (OUT HOLE <<<<<<)
	Quickstart: Azure AI Vision v3.2 GA Read (here IN HOLE)

- Get started with the Azure AI Vision Read REST API or client libraries. 
- The Read API provides you with AI algorithms for extracting 
	text from images and returning it as structured strings. 
- Follow these steps to install a package to your application and 
	try out the sample code for basic tasks.


_ Use the optical character recognition (OCR) client library to read 
	printed and handwritten text from a REMOTE IMAGE. >>>>> 
	The OCR service can read visible text in an image and convert it to a character stream. 

(ASSID)
IF YOU WANT LOCAL IMAGES - see the ComputerVisionClientOperationsMixin methods, 
	such as read_in_stream. 
	Or, see the sample code on GitHub for scenarios involving local images.
(END ASSID)

=== Prerequisites ===
- Azure subsciption,
- Python 3.x

=== An Azure AI Vision resource ===

	You can use the free pricing tier (F0) to try the service, 
	and upgrade later to a paid tier for production.
	The key and endpoint from the resource you create to connect your application to 
	the Azure AI Vision service.

	1) After your Azure Vision resource deploys, select Go to resource.
	2) In the left navigation menu, select Keys and Endpoint.
	3) Copy one of the keys and the Endpoint for use later in the quickstart.

OK, clicking on "An Azure AI Vision resource"
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> (DOWN HOLE)
In Azure
https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision

Page Title: Create Computer Vision
Subscription: Timothy Schofield
Resource Group: Create new CompVisionRGTHX
Region: East US
Name: CompVisionResourceTHX
Pricing tier: Free F0 (20 Calls per minute, 5K Calls per month) - as advised
Check the box: X

Click Review + create button
at review page
Click Create button

Page title: Home > Microsoft.CognitiveServicesComputerVision-20231017090656 | Overview
Your deployment is complete

Click Go to resource

Page title: CompVisionResourceTHX
- As instructed above
	Keys and Endpoint

--------------------------------------------------------------

One of the keys
9408d81f21e44d22bdca59ca7c100feb

Location/Region
eastus

The endpoint
https://compvisionresourcethx.cognitiveservices.azure.com/

--------------------------------------------------------------

(UP HOLE) <<<<<<<<<<<<<<<<<<<<<<<<<<<<
Back i: Quickstart: Azure AI Vision v3.2 GA Read

=== Create environment variables ===

- In this example, write your credentials to environment variables 
	on the local machine that runs the application.

- Go to the Azure portal. 
OK If the resource you created in the Prerequisites section deployed successfully, 
OK select Go to resource under Next Steps. 
OK You can find your key and endpoint under Resource Management in the Keys and Endpoint page. 
OK Your resource key isn't the same as your Azure subscription ID.

- OK alraedy done this bit kind of

- So - I think this where I start a project in VS Code called
==========================================
	VS Code: AzureVisionAPI
==========================================
In AzureVisionAPI
- Create a new Python application file, azure-vision-api.py

Tip:
Don't include the key directly in your code, 
and never post it publicly. 
See the Azure AI services security article for more authentication options like Azure Key Vault.

Well, I will include it for this demo.

To set the environment variable for your key and endpoint, 
open a console window and follow the instructions

Note: SETX writes variables to the master environment in the registry. 
	Variables set using SETX are only available in future command windows and 
	not in the current command window.

In the Terminal

	setx VISION_KEY 9408d81f21e44d22bdca59ca7c100feb
SUCCESS: Specified value was saved.

	setx VISION_ENDPOINT https://compvisionresourcethx.cognitiveservices.azure.com/
SUCCESS: Specified value was saved.

After you add the environment variables, 
you may need to restart any running programs that will read the environment variables, 
including the console window.

Quit VS Code and go back in.

===========================================
==== Read printed and handwritten text ====
===========================================
Back in VS Code AzureVisionAPI
Restart venv

1. Install the client library

	pip install --upgrade azure-cognitiveservices-vision-computervision

2. Install the Pillow library

	pip install pillow

=== Pillow Library ===
The Python Imaging Library adds image processing capabilities to your Python interpreter.
This library provides extensive file format support, an efficient internal representation, 
and fairly powerful image processing capabilities.
The core image library is designed for fast access to data stored in a few basic pixel formats. 
It should provide a solid foundation for a general image processing tool.

3. Create azure-vision-api.py (already done)

4. In azure-vision-api.py copy the follow code

=====================================================================
from azure.cognitiveservices.vision.computervision import ComputerVisionClient
from azure.cognitiveservices.vision.computervision.models import OperationStatusCodes
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

from array import array
import os
from PIL import Image
import sys
import time

'''
Authenticate
Authenticates your credentials and creates a client.
'''
subscription_key = os.environ["VISION_KEY"]
endpoint = os.environ["VISION_ENDPOINT"]

computervision_client = ComputerVisionClient(endpoint, CognitiveServicesCredentials(subscription_key))
'''
END - Authenticate
'''

'''
OCR: Read File using the Read API, extract text - remote
This example will extract text in an image, then print results, line by line.
This API call can also extract handwriting style text (not shown).
'''
print("===== Read File - remote =====")
# Get an image with text
read_image_url = "https://learn.microsoft.com/azure/ai-services/computer-vision/media/quickstarts/presentation.png"

# Call API with URL and raw response (allows you to get the operation location)
read_response = computervision_client.read(read_image_url,  raw=True)

# Get the operation location (URL with an ID at the end) from the response
read_operation_location = read_response.headers["Operation-Location"]
# Grab the ID from the URL
operation_id = read_operation_location.split("/")[-1]

# Call the "GET" API and wait for it to retrieve the results 
while True:
    read_result = computervision_client.get_read_result(operation_id)
    if read_result.status not in ['notStarted', 'running']:
        break
    time.sleep(1)

# Print the detected text, line by line
if read_result.status == OperationStatusCodes.succeeded:
    for text_result in read_result.analyze_result.read_results:
        for line in text_result.lines:
            print(line.text)
            print(line.bounding_box)
print()
'''
END - Read File - remote
'''

print("End of Computer Vision quickstart.")

===============================================================
There are some imports that are paled out
Pylance says they are not accessible:

	VisualFeatureTypes
	array
	Image
	sys

===============================================================
5. As an optional step, see "Determine how to process the data". 
For example, to explicitly specify the latest GA model, 
edit the read statement as shown. 
Skipping the parameter or using "latest" automatically uses the most recent GA model.

e.g.
# Call API with URL and raw response (allows you to get the operation location)
   read_response = computervision_client.read(read_image_url,  raw=True, model_version="2022-04-30")


6. Run the application with the python command on your file.

	python azure-vision-api.py

This is the image file:
https://learn.microsoft.com/azure/ai-services/computer-vision/media/quickstarts/presentation.png

It works! - output in terminal is:
===== Read File - remote =====
9:35 AM
[130.0, 129.0, 215.0, 130.0, 215.0, 149.0, 130.0, 148.0]
Conference room 154584354
[131.0, 153.0, 224.0, 153.0, 224.0, 161.0, 131.0, 160.0]
Town Hall
[545.0, 179.0, 589.0, 180.0, 589.0, 190.0, 545.0, 189.0]
9:00 AM - 10:00 AM
[545.0, 192.0, 596.0, 193.0, 596.0, 200.0, 545.0, 199.0]
Aston Buien
[545.0, 201.0, 581.0, 202.0, 581.0, 208.0, 545.0, 208.0]
Daily SCRUM
[537.0, 258.0, 572.0, 258.0, 572.0, 265.0, 537.0, 265.0]
10:00 AM-11:00 AM
[537.0, 266.0, 590.0, 266.0, 590.0, 272.0, 537.0, 272.0]
Charlathe de Crum
[538.0, 274.0, 584.0, 273.0, 584.0, 279.0, 538.0, 279.0]
Quarterly NI Hands
[538.0, 296.0, 589.0, 296.0, 589.0, 302.0, 538.0, 302.0]
11:00 AM-12:00 PM
[537.0, 303.0, 590.0, 303.0, 590.0, 309.0, 537.0, 309.0]
Bebek Shaman
[538.0, 310.0, 577.0, 310.0, 577.0, 317.0, 538.0, 316.0]
Thuare
[505.0, 316.0, 518.0, 316.0, 517.0, 320.0, 505.0, 320.0]
Weekly stand up
[538.0, 333.0, 582.0, 332.0, 582.0, 339.0, 538.0, 339.0]
12:00 PM-1:00 PM
[538.0, 339.0, 586.0, 339.0, 586.0, 345.0, 538.0, 345.0]
Delle Marckre
[537.0, 347.0, 584.0, 346.0, 584.0, 353.0, 537.0, 353.0]
Product review
[538.0, 370.0, 577.0, 370.0, 577.0, 376.0, 538.0, 375.0]

End of Computer Vision quickstart.

=== Clean up resources ===

If you want to clean up and remove an Azure AI services subscription, 
you can delete the resource or resource group. 
Deleting the resource group also deletes any other resources associated with it.

I think I'll keep them for a while








