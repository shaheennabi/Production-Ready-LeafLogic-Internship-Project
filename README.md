# 🌿 Production-Ready-LeafLogic-Multi-AI-Agents-Project  🌱  

# Problem Statement

***Note:** This project simulates an industry-standard scenario where we, a team of three contributors at XYZ Company, are developing an intelligent system capable of recognizing various plants and crops while providing users with accurate and detailed information about them. This system is designed to prevent misinformation and overpricing in the market by ensuring users can verify plant details instantly.*

## High-Level Note
Concepts related to AI agents, their pipelines, methodologies, and automated research processes have already been discussed. You can find these details in the *docs* folder for further exploration.

## 🌱 Building an Automated Plant and Crop Recognition System 🌿

At XYZ Company, we were tasked with developing a powerful AI-driven system that could recognize up to 100 different types of plants and crops while fetching complete details about them from the internet. The goal was to enable users to identify plants and obtain reliable information effortlessly, reducing the chances of misleading sales practices in the plant market.

### To achieve this, we leveraged a combination of:

- **Object Detection Models**: To accurately recognize various plant and crop species.
- **Multi-AI Agent Pipeline**: Handling detailed research on the detected plant/crop, covering scientific details, historical significance, health benefits, seasonal information, market price, and more.
- **OpenAI Model & UI Integration**: Generates a summary that is sent to the user via email.

## 🌿 Our Role as Developers 🌱

As a team of three contributors, our mission is to build a high-quality, scalable system that:

- Accurately detects a wide range of plants and crops.
- Automates the research process using multi-AI agents to gather real and accurate information from the internet.
- Ensures modular architecture for future scalability and easy maintenance.
- Provides users with trustworthy, well-structured insights on each recognized plant/crop.
- Maintains cost efficiency while delivering high performance and reliability.

This system is designed to deliver seamless plant identification and detailed research findings, ensuring that users have access to verified and comprehensive information. By integrating advanced Object Detection and AI Agents, we aim to create a reliable solution that empowers users with knowledge and prevents misinformation in the market.


## 🎯 Project Goals

### Deliver a System to Meet Client Needs

- Create a system to provide accurate and instant plant/crop insights, ensuring users receive reliable information.

### Build a Modular Architecture

- Design the system to support scalability and future upgrades, making modifications seamless.

### Testing and Quality Assurance

- Ensure the system’s reliability and accuracy through rigorous testing.

### Cost-Effective Measures

- Optimize API and model-related costs without compromising performance.

---

## ✨💚 Our Approach

The project followed a structured approach to ensure efficient execution and high-quality results.

### Steps in Our Approach

#### Framework Selection

- Chose **Object Detection with YOLOv5**, trained on an **A100 GPU** with a **self-annotated dataset** of **25K images**, which we later **open-sourced** for the community.
- For our AI Agents pipeline, we used taskflowai framework to build these (as it's high-quality open-source support framework-used to build Multi_AI Agent system).

#### Model Selection

- **OpenAI GPT-3.5 Turbo** was chosen for:
  - **Tool-Calling Excellence**: Best ability to call tools and manage multi-agent tasks seamlessly.
  - **Token Management**: Optimized context handling and reasoning.
  - **Report Generation**: Efficiently processing and generating structured reports.
- **LLaMA & Google Models** were tested but failed in reasoning, token management, and memory performance.
- **OpenAI o1 Series Models** offered strong performance but were too expensive for this project.
- **Groq Inference Engine** was also tested but did not meet the project’s performance indicators.

#### System Design and Development

- Built a **modular architecture**, ensuring future adaptability and easy upgrades.

#### Cost Optimization

- Minimized API costs by **choosing GPT-3.5 Turbo** and optimizing system performance.

#### Testing and Delivery

- Conducted rigorous testing to validate accuracy and reliability (in containers etc).
- Delivered the project on time, exceeding quality expectations.

---

## 🚧 Challenges Encountered

### 1. Model & API Cost

- Running inference and AI agents on cloud infrastructure was **cost-intensive**, requiring optimizations.

### 2. Training on a Large Dataset

- **Training on 25K images** with **A100 GPU** required substantial resources and time.

### 3. Cloud Inference Costs

- Running the **entire pipeline on the cloud** every time a user pings the system was expensive.

### 4. Data Annotation Process

- Annotation of **25K images** was a **time-consuming** and **labor-intensive** task, requiring collaboration among contributors.

## 🌟 How We Fixed Challenges  

#### Solutions Implemented

- **Choosing GPT-3.5 Turbo**: A cost-effective alternative while maintaining performance.  
- **Optimizing Training**: Trained the model on **100 epochs** with **25K images**, which was computationally intensive but proved effective.  
- **Cloud Cost Optimization**: Leveraged **EC2 instances** to fetch the **Docker image** from **ECR**, reducing overall cloud costs.  
- **Efficient Data Annotation**: Two contributors collaboratively annotated the dataset, significantly reducing annotation time and effort.  


---
## 🌿 System Design or Project Pipeline  

To simplify the complexity of our pipeline, we divide it into two main components:  

1. **Training Pipeline**  
2. **Prediction Pipeline + AI Agents**  

### 🔧 Training Pipeline

*This is how **Training Pipeline** looks like*  

![CI_CD Diagram](https://github.com/user-attachments/assets/6edb279e-2b16-4c26-a478-d43e31d3dcb2)


First, we will retrieve data from **S3** as part of the **Data Ingestion Process** using this script from `utils`.  


<img width="820" alt="s3_config" src="https://github.com/user-attachments/assets/2ec531d4-c595-4762-9e7f-53e7a69a33a0" />

- The `download_file` method:
  - Retrieves the file size using `head_object` for accurate progress tracking.
  - Uses `TransferConfig` to enable efficient multipart downloads (5MB chunks).
  - Implements a **progress callback** (`ProgressPercentage`) to log real-time updates.
  - Logs the start and completion of the download process for better visibility.
  - Handles errors gracefully by raising a `CustomException` on failure.

- The `run()` method acts as an entry point to execute the download seamlessly

###  Data Ingestion

After downloading the dataset from **S3** as `leaflogic_dataset.zip`, it is stored in the **data_ingestion_dir**. The script then extracts the dataset into the **feature_store_path**, ensuring the data is properly organized for further processing.

*This is the shot of only `initiate_data_ingestion` for more go to `src/leaflogic/components/data_ingestion`
<img width="815" alt="Screenshot 2025-03-01 101319" src="https://github.com/user-attachments/assets/3a41a758-72b1-4f56-8a89-8e77b4faf17f" />


- **Initialization (`__init__` Method)**:  
  - Sets up the data ingestion directory.  
  - Logs initialization status.  

- **Data Download (`download_data` Method)**:  
  - Downloads the dataset from **S3** and saves it as `leaflogic_dataset.zip`.  
  - Uses **S3FileDownloader** to fetch the file.  

- **Data Extraction (`extract_zip_file` Method)**:  
  - Extracts `leaflogic_dataset.zip` into a temporary directory.  
  - Moves only the relevant dataset (`leaflogic_dataset`) into the **feature_store_path**.  
  - Cleans up temporary files after extraction.  

- **Data Ingestion Pipeline (`initiate_data_ingestion` Method)**:  
  - Calls the download and extraction methods in sequence.  
  - Returns a **DataIngestionArtifact**, storing paths to the downloaded and extracted dataset.  
  - Ensures proper logging and exception handling to track failures efficiently.  


### Prepare  BaseModel

After **data ingestion**, we prepare the base model by configuring `yolov5s.yaml` into `custom_yolov5s.yaml`. This involves updating the **number of categories (nc)** from `data.yaml` and defining essential parameters such as the **backbone, head, and other configurations** for training.

<img width="818" alt="Screenshot 2025-03-01 101223" src="https://github.com/user-attachments/assets/fa500189-bba4-4bc5-9a09-253a3f49af7f" />

- **Initialization (`__init__` Method)**:  
  - Loads the data ingestion artifacts.  
  - Locates `data.yaml` to retrieve the number of classes (`nc`).  
  - Ensures the file exists before proceeding.  

- **Updating Model Configuration (`update_model_config` Method)**:  
  - Reads `data.yaml` to extract the number of categories.  
  - Loads the base YOLOv5 model config (`yolov5s.yaml`).  
  - Updates the `nc` field along with other essential configurations.  
  - Saves the modified configuration as `custom_yolov5s.yaml`,  
    but for **preserving the original structure**, we have written a custom `write_yaml_file` function in `utils`.  
    When modifying the `nc` parameter, the default YAML formatting would break, so this function ensures the correct structure is maintained.
  
*these are the shots of `write_yaml_file` for maintaining structure from `utils`  
<img width="813" alt="wt" src="https://github.com/user-attachments/assets/ced94e0e-b236-4f3b-a0db-86ab8222b544" />
<img width="818" alt="wr1" src="https://github.com/user-attachments/assets/cea223d0-b84f-4ea5-abf0-ed9ea9272c84" />
<img width="812" alt="wr2" src="https://github.com/user-attachments/assets/184f09f6-db55-4b34-85f1-e11298f1d07e" />

*back to `prepare_basemodel`
- **Model Preparation (`prepare_model` Method)**:  
  - Calls `update_model_config()` to generate the custom YOLOv5 config.  
  - Returns an artifact containing the path to the updated configuration file.  
  - Ensures all changes are logged for tracking and debugging. 

### Model Trainer
A






## License 📜  

This project is licensed under the **MIT License**.  
Feel free to use, modify, and share it with proper attribution. For more details, see the [LICENSE](LICENSE) file. 🌟  ....

