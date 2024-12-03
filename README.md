# Workout Generator: A Marathon Training App

## **Overview**
This application is designed to help me train for a marathon. The Transformer model, trained on my own Strava data, is designed to predict the pace of my next run based on the previous week's workouts (paces and distances). From this pace prediction, a distance is assigned based on the previous week's mileage and the predicted pace (for example, an 8:30 min/mile pace would correspond to a shorter distance run, while a 10:45 pace would correspond to a long run). The final output of the model is a running workout with a suggested pace (generated by the model) and time.

## **Data**

I collected my data from the fitness app Strava, which is popular among the running community. Strava provides detailed summaries of each workout, including effort scores, grade-adjusted pace, heart-rate zones, and more. It also offers aggregated workout summaries, the ability to connect with other users and join groups, and even beta AI features available to Pro users, which generate text summaries of each workout.

![image](https://github.com/user-attachments/assets/6e23f770-311a-424d-8ce5-caa8ef9b4894)
***Fig 1.1: Strava App Interface ([source](https://www.google.com/url?sa=i&url=https%3A%2F%2Flifehacker.com%2Fhealth%2Fstrava-is-the-best-running-app-review&psig=AOvVaw2nOBRXDAukmtOjqx3hSpoi&ust=1733281709274000&source=images&cd=vfe&opi=89978449&ved=0CBQQjRxqFwoTCLicg9nPiooDFQAAAAAdAAAAABAh))***

Since Strava does not natively support exporting data in a .csv format, I used a workaround to extract my data in this format. This [article](https://scottpdawson.com/export-strava-workout-data/) outlines the method for exporting Strava data to .csv.

The raw data I extracted contained 51 columns, offering comprehensive details—from Twitter links to post workouts to total mileage. For this task, I focused on the workout details, which included the total moving time, the date of the workout, the time of day, total miles, workout type, and elevation gain. With these variables, I calculated pace by dividing moving time by the total distance. Pace serves as the target variable for this task.

I filtered the raw data to exclude workouts with pace times over 20 minutes, as these were outliers in an already small dataset and likely do not represent actual running workouts. I also removed non-running activities, such as swims and walks. After filtering, I scaled the three key numeric variables: pace, distance, and elevation gain. Scaling was essential here because elevation gain, in its raw form, has a much larger scale than pace or distance. Using the raw data could have decreased model performance by diminishing the accuracy of predictions.

## **Model Details**
While transformer models are most commonly used for their applications in NLP tasks, this task involves a less common usage of transformers: continuous variable prediction. My Strava data is nonlinear, sequential (time-series), and exhibits micro-seasonality, with each week including longer and shorter runs that progressively increase in distance and speed as training continues. The complex nature of this data justifies the use of a more sophisticated prediction model, which is where transformer architecture comes into play. The attention mechanism in transformers makes them well-suited to handling sequential data and generating predictions. Although transformers are more computationally intensive than simpler models, such as multiple linear regression, my dataset is relatively small, so the computational load of training a more complex model remains manageable.

To perform this task, I am using a simple Encoder-Decoder Transformer architecture. The encoder processes the inputs and extracts features, while the decoder generates predictions based on these features. The model incorporates a self-attention mechanism and multi-head attention, which are key to its effectiveness for this task. The self-attention mechanism allows each token to attend to every other token in the sequence, capturing dependencies within the data—ideal for time series. The multi-head attention mechanism enables the model to focus on different parts of the sequence simultaneously.

However, Transformers do not inherently handle the order of tokens, which is important for identifying patterns in sequential data. To address this, a positional encoding is added to the input data, allowing the model to understand the relative positions of tokens within the sequence and capture position-dependent patterns.

![image](https://github.com/user-attachments/assets/2737c904-8686-436c-93bf-d2ba9b0d4abd)
***Figure 1.2: An Encoder-Decoder transformer architecture ([source](https://blogs.nvidia.com/blog/what-is-a-transformer-model/))***

Model Parameters:
- Input Dimensions: 5 features per data point (distance, pace, time of week, workout type, elevation gain)
- Hidden Dimensions: 128 (large hidden layer size increases model capacity)
- Attention Heads: 8 (allows the model to focus on different parts of the input sequence)
- Number of Layers: 4 (allows the model to capture complex relationships in the data
- Output: A single value (output_dim = 1), representing the predicted pace

## **Analysis**
<img width="1250" alt="Screenshot 2024-12-02 at 9 16 40 PM" src="https://github.com/user-attachments/assets/2741ba10-2141-4ba4-b2b4-9c99b546b909">

***Figure 1.3: Gradio-powered interactive workout generator***

With only 107 valid running workouts as context, I trained an encoder-decoder model to predict the pace of my next run and derive an appropriate distance from that pace, allowing me to suggest a workout plan that reflects my recent training. Despite the small training dataset, the model performs reasonably well and generates plausible predictions. While this task is relatively simple (it provides only one workout, with pace and distance), this approach builds on algorithms already in use by apps like Runna and highlights the potential of transformer architecture in creating personalized workout plans.

After 200 epochs of training, my model reached a training loss of 0.397. However, the test loss was notably higher, at 0.704. This difference suggests potential overfitting, which makes sense given the small dataset size. At the same time, the data’s complex sequential patterns may be challenging for the model to learn, suggesting underfitting. Nevertheless, when I increased the number of epochs to enhance the model's training capacity, the test loss continued to grow, indicating that overfitting due to the limited data size is likely the primary issue.

Due to Strava’s current limitations on data export, it’s impossible to collect large batches of running data from public users. Instead, I exported my personal data, which contained only around 200 workouts. After filtering out walking and swimming sessions, I was left with 107 running workouts. Given the discrepancy between training and test loss, it's clear that this isn’t enough data to achieve optimal model performance. However, if I could gather a larger dataset from Strava users, I could train the model on that data and make predictions based on the trends observed in other runners. This might be feasible through web scraping.

## **Future Directions**

The application of transformer models in workout planning holds significant potential for expansion, enabling functionality that could rival platforms such as Nike Run Club or Runna. Given the current capabilities of transformer models—particularly in tasks such as NLP—I propose the following enhancements:

- **Web Scraping for Data Collection:** Utilize web scraping techniques to gather extensive datasets from platforms like Strava, enabling the training and fine-tuning of transformer models to identify patterns in marathon training workouts.
- **Expanded Use of Transformers for Continuous Variable Prediction:**
  - Extend the model's capability to predict continuous variables, such as distance, heart rate, and expected effort level, which could be used to refine workout suggestions.
- **Fully Developed Workout Plans:**
  - Expand the current model to generate comprehensive workout plans, spanning from the start of race training to race day, complete with detailed workout descriptions.
  - Incorporate large language models (LLMs) to adapt workout plans based on user preferences, enabling interaction with a "coach" to adjust the plan as needed.
- **Integration with Google Maps API:** Provide suggested routes for each run, tailored to the user's current location, target distance, and target elevation, by leveraging the       Google Maps API.
- **Integration with Google Calendar API:** Offer personalized workout time suggestions based on the user's availability and the estimated duration of each workout, using the Google Calendar API.
- **Export Functionality:** Allow users to export the generated routes, calendar files with workout times, and the full training plan in various desired formats for ease of use and sharing.

## **Model Card**

#### **Uses**
This model, trained on the user's historic workout data, is designed to generate personalized marathon training workouts based on a user's inputted data for their previous week of workouts. It predicts the pace of the next run and uses that prediction to assign a distance, resulting in a tailored workout plan. It is particularly useful for runners who want to structure their training with specific pace and distance goals.

#### **Sources**

##### **Data:**
- Collected using workaround gathered in this [article](https://scottpdawson.com/export-strava-workout-data/)

##### **Code:**
- Usage of Huggingface Transformers library

##### **Model:**
- Usage of vanilla encoder-decoder transformer architecture described in Vaswani et. al's ["Attention Is All You Need"](https://arxiv.org/abs/1706.03762) 2017 article
- Trained on personal data
  
#### **Permissions**
- Data is compliant with Strava's [Terms of Service](https://www.strava.com/legal/terms) for usage of personal data
  - Usage of web-scraping to expand dataset using public Strava data would fall under different guidelines
  
#### **Code Structure**
- 

## **Resources**
