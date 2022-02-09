# MLOps in Azure FAQ

The contents below are answers to questions raised on the chat of the *"Microsoft Tech Briefings: Put MLOps into Practice"*, streamed on the 9th of February 2022 and presented by [João Pedro Martins](https://www.linkedin.com/in/joaopedromartins/) and [Xiaopeng Li](https://www.linkedin.com/in/xiaopeng-li/), available [here](https://event.on24.com/eventRegistration/console/EventConsoleApollo.jsp?uimode=nextgeneration&eventid=3572037&sessionid=1&key=E9995E2F096823571711AA431CA86EB2&contenttype=A&eventuserid=305999&playerwidth=1000&playerheight=650&caller=previewLobby&text_language_id=en&localeCountryCode=UK&format=fhvideo1&newConsole=true&newTabCon=true) for offline viewing. The slides are available in this same repo, [here](https://github.com/lokijota/azure-mlops-faq/blob/main/2022%20Feb%20Tech%20Briefing%20-%20MLOps.pdf).

## What format do you recommend for the trained model?

I can't really say there's a specific format that would be recommended over any other. Frameworks like PyTorch/Keras/TF use their own specific formats, Scikit Learn uses PKL, you may have specific formats for edge deployments. We'd suggest you use what's faster to delivering value for your use case. If you are looking for unified/single format or performance optimizations, the clear recommendation is to try ONNX.

## How many roles do you need to cover entire lifecycle ? Do you see those roles converging ?

As Jota is talking about now, we need a mix of competence including data science, data / ML engineering, software / DevOps, infra & security to close the loop of MLOps. In addition, domain knowledge and business understanding are crucial to ML solution development and production as well. We do see a blurred line sometimes between data scientist and ML engineer. But it really depends on individual competence and preference. MLOps is a team sport so what matters is we have all needed skills as a team.

## Why is git not good to version Data snapshot?

One of the reasons is that Git has limits on the size of the files you check-in. Also, it was built for code/text assets, and data snapshots are usually binary files (eg, database snapshots or parquet files, not CSVs). It can obviously be used for any type of file, but it just wasn't designed to deal with data blobs.

## In your experience, what is the "best" model interpretability method for ANN's? Could we receive the slides and links to relevant documentation after your session?

It's not a simple question, but if I had to pick one, SHAP would be the first choice. Check https://github.com/slundberg/shap or the RAI toolbox for capabilities in this area: https://github.com/microsoft/responsible-ai-toolbox

## Is there an Azure tool for data versionning ?

Check out this documentation on data versioning with Azure Machine Learning Datasets, which support versioning: <https://docs.microsoft.com/en-us/azure/machine-learning/how-to-version-track-datasets>

## Would MLOps be any different if I were to use it for Deep Learning based models? (a Compyter Vision based Object Detection model for instance). Does Azure support resgistering, deploying and inferencing Pytorch models?

The main differences with DL learning models are related to volume -- data can be larger, training/tuning can take days, and the generated models are often Gb-sized, impacting the design of deployment options. The design would thus have to be slightly different, considering distributed training, GPUs, etc. As to the 2nd question, yes, we support all of that for both PyTorch and TF/Keras, and we also have a partnership with Meta to offer technical support for PyTorch via the Azure Portal, by Microsoft Support Engineers.

## Are there any special consideration when running models on the Edge?

This is a particularly interesting challenge. I assume you're talking about computer vision models, which is the most common scenario we see. The models will often have to be quantized (for size/thus loosing quality) or converted to formats that are suitable to the hardware you have in place, there can be some trial and error due to the hardware specificities. That said, from the MLOps size the process doesn't change much, you just have to add required conversion steps and deployment via IoT Hub or the IoT platform you're using.

## Is there an additional advantage of integrating MLFlow with Azure ML when already using Azure ML and Azure DevOps?

One of the main reasons we see Customers using MLFlow is the preference for an OSS platform, or wanting to use the same software across different services (in Azure, these would be Azure Machine Learning and Databricks). We try to give the same experience both with the AML Python SDK and MLFlow, as possible.

## Would you ever recommend to do the decision wether a newly trained model should replace the actual deployed one by a human?

You should *always* do proper testing before deploying any morel. For any scenario where there's risk related to fairness, we believe it's essential to always have a Human in the Loop, and opting for "Human Augmentation" (vs Replacement) scenarios. This is a wider question and has societal implications, as you are I'm sure aware.

## Azure DevOps is known and popular on market, any similar MLOps product now?

At the moment we're trying to have Azure DevOps and GitHub Actions on par in capabilities, and the new Azure ML CLI/SDK will be well suited for both, using Yaml pipelines. The focus is thus on having capabilities in these services that allow us and customers to build succesfull MLOps implementations -- without reinventing the wheel :-)

## How can we capture various metrics in MLOps?

Both the MLFlow and AML Python SDK allow for metric tracking, which include key-values and even files, images, etc., as part of the outcome of an experiment.

## Do you need multiple ML Workspaces?

This is in fact frequent, yes, and it's present in our documentation. I (Jota) often see customers using one per environment (exploration, qa, prod, etc.), and sometimes these are also sliced by business area. This is part of the design and customer choices, not a requirement from Azure's part.

## I have a question regarding Model Validation and Model Deployment. In reality you want to have Pipelines that run automatically, so train models on new data, validate them etc. but we also want to automatically deploy a new Model if it is "better" then the actualc deployed one. Is there any best practice to do this Model Evaluation before deploying? Where should this part happen?

Great question. There are several options -- I like to do it as part of the model training pipeline. Not only doing things like comparing metrics, but also running "unit tests" against pre-sampled data, to compared data distributions, and also doing error analysis. This is not simple and depends on the scenario, but just after model training is the right place to do it.

# How is the responsible AI integrated in MLOps? by mlops dashboard?

Azure ML has fairness capabilties built-in, but please check this repo for the most up to date features: https://github.com/microsoft/responsible-ai-toolbox . These features are being integrated into Azure Machine Learning.

# How can I visualize model performance metrics of models that are in production

That's a different type of tracking, as you don't have the ground truth. You have to capture ongoing inferences, and then use whatever method is adequate to your scenario to explore this and determine if there's model degradation. For visualization any tool would do (obvs I'd recommend PowerBI) ;)

## When should we use Azure Data Factory or Azure ML pipelines to implement data transformation steps?

The recommendation is to use ADF for data flows between systems, and AML PIpelines for data transformations (the data science work).

## Is there a path defined for trainings particularly for AML and MLOps? Which one should be first and then the next steps...

I would suggest looking at the mlopspython github repo for a reference implementation, and this mslearn course for the new SDK: https://docs.microsoft.com/en-us/learn/paths/train-models-azure-machine-learning-cli-v2/

## Q: How do you setup MLOps and CI/CD in Azure Machine Learning?

A: The architecture presented on during the session prescribes using Azure ML for the "inner loop" of the Data Science/Machine Learning work, using concepts like Pipelines, Datasets, Experiments or the Model registry. For the "outer loop", when you go to training at scale and automated deployments, the tooling we recommend is either Azure DevOps or GitHub Actions, which allow you to develop these end-to-end pipelines. 

Some suggested startings points are https://github.com/microsoft/MLOpsPython for a reference implemenation with Azure DevOps, and for upcomgn 2.0 AML CLI/SDK, this training covers the essential pieces of the journey: https://docs.microsoft.com/en-us/learn/paths/train-models-azure-machine-learning-cli-v2/ . Another recommended content is the guidande for a secure setup of AML, available here: https://github.com/jhirono/amlsecurity .


## Q: What about A/B testing to evaluate a model?

A: Doing A/B testing of new models is actually highly recommended, but after the models have gone through an offline evaluation/error analysis phase against test data. A/B testing is supported out of the box in AML by leveraging the capabilities in Azure Kubernetes Service, see this documentation page: https://docs.microsoft.com/en-us/azure/machine-learning/how-to-deploy-azure-kubernetes-service?tabs=python#deploy-models-to-aks-using-controlled-rollout-preview .

## Q: How is the data drift handled in MLOPs Deployment?

A: AML includes the concept of Dataset monitors to address data drift. See here for more information: https://docs.microsoft.com/en-us/azure/machine-learning/how-to-monitor-datasets?tabs=python . In terms of where it fits into an MLOps architecture, it's part of the "Monitor" capabilities, an when fully automated, will generage retraining triggers.


## Q: What is the role of the feature store in MLOps and what is the best option to hosting a feature store with Azure ML services?

A: Customers with a high level of maturity and level of reuse of datasets/features will often put in place a Feature Store capability as part of their solutions. Our recommended approach on how to do this in Azure relies on Feast, the open source Feature Store. See more information about this here: https://techcommunity.microsoft.com/t5/ai-customer-engineering-team/bringing-feature-store-to-azure-from-microsoft-azure-redis-and/ba-p/2918917 .



## Q: Data scientists want to use live production data, but in a typical development environment this is not available. For several reasons you usually don't want to develop models on the production environment either. Any advice on this topic?

A: This is a complex problem. One approach is to have a representative subset of the data (with a sampling method that makes sense for the problem at hands), available in the exploration environments, and then data scientists submit training jobs to run on a more controlled environment with access to full datasets and more compute capabilities. They would get the results back and decide on further steps. This does impact productivity, however. Another option would be to set up a data extraction process from producton that de-identifies data if needed, and makes it available in an exploration environment, but this environment would have to be fully controlled and locked down to minimize risk of data exfiltration/save to local.


## Q: Any tips on how to test an ML model before deployment?

A: Simpler approaches to model evaluation proir to deployment usually mention comparing metrics like AUC or Accuracy, which is not sufficient for real scenarios. Two recommendations are to include sample datasets and look at the distribution of the inferences, and in particular the distribution of errors. Highly recommend the Error Analysis capabilties in teh Responsible AI Toolbox to assist in this: https://github.com/microsoft/responsible-ai-toolbox/blob/main/docs/erroranalysis-dashboard-README.md .


## Q: What about storing ML Models in SQL Databases like SQL Edge DB, Doesn't this make the deployment/update easier, or are there limitations compared to docker containers?

A: This was in reference to a previous question on Edge AI scenarios. One of the key reasons to use containers for ML in general is related to the interdependency of Python libraries and their versions (Eg, Pandas, Scikit Learn, PyTorch, etc.), which give a similar capability to what Anaconda environments give, but for production deployments. The issue is not on where to store models, but how to run them with the right dependencies in place. As to making deployment easier, I do recommend checking out Azure IoT Hub and in particular Azure IoT Edge: https://docs.microsoft.com/en-us/azure/iot-edge/about-iot-edge?view=iotedge-2020-11 , which make this very simple. Much of the complexity is not on how to deploy/update edge containers, but getting the models to run on a diversity of edge hardware and softwre.



## A: Where can we find other reference archtitectures for MLOps?

Q: Please check out these sources as starting points:

- https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/ai/mlops-python
- https://github.com/Microsoft/MLOpsPython
- https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/ai/orchestrate-mlops-azure-databricks
- https://docs.microsoft.com/en-us/azure/architecture/example-scenario/mlops/mlops-maturity-model
- https://github.com/microsoft/dstoolkit-mlops-base
