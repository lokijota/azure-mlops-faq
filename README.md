# MLOps in Azure FAQ

The contents below are answers to questions raised on the chat of the *"Microsoft Tech Briefings: Put MLOps into Practice"*, streamed on the 9th of February 2022 and presented by João Pedro Martins and Xiaopeng Li, available [here](https://event.on24.com/eventRegistration/console/EventConsoleApollo.jsp?uimode=nextgeneration&eventid=3572037&sessionid=1&key=E9995E2F096823571711AA431CA86EB2&contenttype=A&eventuserid=305999&playerwidth=1000&playerheight=650&caller=previewLobby&text_language_id=en&localeCountryCode=UK&format=fhvideo1&newConsole=true&newTabCon=true) for offline viewing.


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
