# Voder
> Compating Identity Politics, One Swipe At A Time.

## Getting started
Welcome to Voder, an online platform of presendential information to help voters across America make an informed decision during the 2020 election!

## The Code
The code we used is centered around the `SlideableCards.vue` file. The software that we used was Vue.js and Yarn, both of which helped greatly in the development of our Javascript project. We also downloaded 'npm install --save sweetalert' to allow us to present aesthetic alert boxes for our users after every 10 cards.

The goal of Voder is to act as a source of information in an interactive way. We use a deck of 60 cards, wherein the user can decide to either accept, skip, or reject that card. Each card is based on a certain topic (10 categories in total), and there are 6 cards per category. These cards describe the stances of the presendential candidates (Trump and Biden), and the user can decide whether or not he/she agrees, disagrees, or feels indifferent about the candidate's stance.

The cards were developed via Figma, and uploaded to our files in the `assets/images` file. They were then brought into the code as JSON objects, where we included the category that they belonged to for future development in this project. 

To avoid any bias, and to allow our users to use this application more than just one time, we developed a shuffling algorithm for our cards. This algorithm essentially randomizes the cards each time, but not to the extent where a particular category dominates a round of results. We shuffle these cards based on category (as in, we shuffle the 6 cards of the same category seperately from another 6 cards). Then, we make sure that at least 1 card from each category is represented after each round, just so that our users can get a well-rounded detailed report on what they believe in across all topics. 

We had several event handlers that dealt with button functionality as well as swiping functionality. They calculated the results and recorded each card's index and src. This is to ensure that we are not making any errors in recording our data and so that we can be efficient once this project grows to emcompass more than just 60 cards. The event handlers we used were MATCH, REJECT, INFORMATION, SKIP, and REFRESH.

## The Design
We know that user interface is very important to people, which is why we spent a lot of time on 'SlideableCards.vue' trying to make our design the best and most interactive. We decided to approach this using Figma for our logo and icon, as well as many CSS and SCSS features to give a simplistic, yet effective, design on Voder.
