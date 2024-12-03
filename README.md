# Workout Generator: A Marathon Training App

## **Overview**
This application is designed to help me train for a marathon. The Transformer model, trained on my own Strava data, is designed to predict the pace of my next run based on the previous week's workouts (paces and distances). From this pace prediction, a distance is assigned based on the previous week's mileage and the predicted pace (for example, an 8:30 min/mile pace would correspond to a shorter distance run, while a 10:45 pace would correspond to a long run). The final output of the model is a running workout with a suggested pace (generated by the model) and time.

## **Data**


## **Model Specifications**
While transfomer models are most commonly used for their applications in NLP tasks, this task involves a less-common usage of transformers: continuous variable prediction. The nature of my Strava data is nonlinear and is sequential (time-series). It also has aspects of micro-seasonality, as each week has longer and shorter runs within the week, progressing longer and faster over time as my training continues. The complex nature of this data justifies the usage of a more complex prediction model, which is where the usage of transformer architecture arises. The attention mechanism used in transformers makes them well-equipped to handle sequential data and generate predictions. While transformers are heavier models than a simpler model, like a multiple linear regression, my dataset is relatively small, meaning the computational load of training a more complex model with this data would also be relatively small.

See specific model specifications below:
-
