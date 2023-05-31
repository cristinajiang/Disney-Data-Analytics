# Disney-Data-Analytics
# Project 1: Intro 
  In this project, I ran analytics using Python and Excel to investigate and answer the four research questions. Throughout this process, I performed data cleaning, A/B testing, assumption testing, data normalization, and various statistical analytics method including one factor ANOVA test, heterogenity testing, Tukey test, and variance/Barlett test. At the end, I gave recommendations on the actionable steps Disney can take to improve their performance and increase their competitiveness. 
  
  #It is important to note that I only include part of the entire project in this portfolio, I would be happy to share the entire and more detailed project if asked 

# Problem Statement: 
  Recent news sprang to light when Disney reported fourth-quarter earnings that missed expectations, driving its stock price down. With rising competition from Netflix and Hulu, it is crucial to examine if any specific variables affect the media giant’s top-line success. My main point in this project is to answer one essential question: what factors contribute to the success of Disney’s two highest revenue streams: movies and streaming?
  
# Dataset: 
  I gathered a total of three datasets. Dataset 1 contains the primary variables related to a movie’s box office success or gross revenue generated from Kaggle, with its primary source of data being the box office data of Walt Disney Studios from 1937 to 2016. Datasets 2 and 3 were given to me by Professor Orkun Baycik from Boston University’s Questrom School of Business and details attributes of “Disney+’ subscribers and Hero/Villian preferences. 
  
# Data Cleaning: 
  I first started off by doing data cleaning, example code I used: 
  
  disney = pd.read_csv("disney_movies_total_gross.txt", delimiter=('\t'))
  #change type to float now or int, info says int
  disney["total_gross"] = disney["total_gross"].str.replace('$',"").str.replace(',',"")
  disney["total_gross"] = pd.to_numeric(disney["total_gross"]) 

  #also do it for inflation_adj_gross
  disney["inflation_adjusted_gross"] = disney["inflation_adjusted_gross"].str.replace('[\$\,]',"",regex =True)
  disney["inflation_adjusted_gross"] = pd.to_numeric(disney["inflation_adjusted_gross"])

  #dropping the rows with nan
  disney.dropna(inplace=True)

  pd.set_option('display.max_rows', None)
  pd.set_option('display.max_columns', None)
  #print(disney)
  #Data is now cleaned and ready for use

# Data Normalization: 
  To ensure that the data is sufficient and ready to use, it needs to meet the assumption test. To do so, I performed data normalization. Example code I used: 
  #Test for normality using Shapiro Wilk Test:
    shastat, shapval = (stats.shapiro(disney['inflation_adjusted_gross']))
    print('pval for shapiro:', shapval)
    if shapval <= 0.05:
      print("We reject null and conclude that there is evidence to support the fact that the data is not normally distributed.")
  else:
      print("We fail to reject null and cannot conclude that the data is not normally distibuted so the data is normally distributed.")

  #failed the normality test so we will need to normalize it:  
  disney['log'] = np.log((disney['inflation_adjusted_gross'])) 
  disney['cube'] = (disney['log'])  ** 3
  print(disney.agg(['skew', 'kurtosis']).transpose())
  #after normalizing we see that the skew for the cube column is -.247 so its normal    

  disney['cube'].hist(grid=False,
          figsize=(10, 6),
          bins=30)
  #plot the graph to see distribution
  rows = disney.query('genre == "Adventure"')
  r = rows["cube"]
  rows1 = disney.query('genre == "Comedy"')
  r1 = rows1["cube"]
  rows2 = disney.query('genre == "Drama"')
  r2 = rows2["cube"]
  rows3 = disney.query('genre == "Action"')
  r3 = rows3["cube"]
  rows4 = disney.query('genre == "Thriller/Suspense"')
  r4 = rows4["cube"]

  
 # One of the four research questions I had was "Does genre impact Disney’s Movies Gross Income?" 
      If there is data to support the fact that one genre consistently performs better on inflation-adjusted gross compared to another genre, it is only natural for Disney to take advantage of that fact to maximize profit. This experiment is even applicable to the opposite scenarios. If there isn’t a difference in the average inflation-adjusted gross when changing the genre, Disney directors don’t have to worry about what type of movies they should film. Knowing that a change in one genre to another will not lead to such a significant difference in the mean inflation-adjusted gross, they have free range in all movie genres, with little financial pushback.

  To do so, I ran the One-Factor ANOVA test on Python, where I got the image below as a result: 
<img width="472" alt="Screenshot 2023-05-30 at 9 58 48 PM" src="https://github.com/cristinajiang/Disney-Data-Analytics-Python/assets/135065815/d010fa33-61f7-47f1-91ac-d2bbbd97f4ac">

  As seen in the table, with a p-value of 0.000035, at a significance level of 0.05, I can reject the null hypothesis, meaning that there exists a difference in the average inflation_adjusted_gross as the genre changes. To determine which genres exactly are the top performers. To do so, I ran the Tukey Test. The result is shown below: 

<img width="755" alt="Screenshot 2023-05-30 at 10 04 29 PM" src="https://github.com/cristinajiang/Disney-Data-Analytics-Python/assets/135065815/fdf6f0b4-5fc5-4158-9507-0ceb83cf2565">

  I found that since genres action, adventure, musical, romantic comedy, and western, thriller/suspense performed the best out of 12 genres in the “Disney Movies Total Gross” dataset, Disney should produce the majority of its movies under these 6 genres. 

  Though I was able to conclude that Action, Adventure, Musical, Romantic Comedy, Thriller/ Suspense, and Western were the genres that perform better in terms of inflation-adjusted gross. There is one drawback to the experiment, I was unable to narrow it down to fewer genres. So, my conclusion is very broad.  One way to address this is to add another treatment. It would also be interesting to see if the ratings of each movie may also play a role in the gross and genre. This would mean we need a two-factor Anova test, as I consider two treatments: genre and MPAA_rating. This would hopefully give Disney directors a more defined set of parameters to consider when filming. In other words, they would theoretically be able to see which genre and rating they should focus on to get the best outcome of gross.


# "Is there a relationship between implementing a generic recommendation system and the number of Disney+ subscribers?" 

  Disney created a simple generic recommendation system in response to the pressure. The company must utilize experimental methods to determine whether this simple feature contributes to the subscriber counts. The goal of this experiment is to evaluate the A/B testing result and measure the impact of a new generic recommendation system on the number of disney+ subscribers. 
  
  To do so, I ran a linear regression test, and got the following result <img width="624" alt="Screenshot 2023-05-30 at 10 16 24 PM" src="https://github.com/cristinajiang/Disney-Data-Analytics-Python/assets/135065815/c5276526-85fa-4c3e-a579-e525f56b1f90">

   It is important to note that the p-value of this regression is 0.204. The new p-value is lower than the previous p-value of 0.565 yet it is still above the desired alpha value of 0.05. This indicates that the new results are not statistically significant. The standard error for the independent variable is 0.019 compared to the 0.019 from the first regression. Much like the prior regression, an std error lower than 0.05 indicates that our regression is representative of the entire population and that replicating this experiment would yield the same results. The adjusted r-squared value is 0.003, which means that 0.3% of the variance in the Disney+ subscriber count can be explained by our variables [DisneyPlus Recommendation System_Yes] and [Average Annual Income (in thousands)]. The low r-squared value means a high variance around the regression line and we need to explore other factors that can influence a region’s choice in subscribing to Disney+.

  To test for a more accurate result, I also used DoWhy method, and got the following result: 
  <img width="585" alt="Screenshot 2023-05-30 at 10 19 38 PM" src="https://github.com/cristinajiang/Disney-Data-Analytics-Python/assets/135065815/cbd16ba7-0dad-4011-8b5d-e5fcea2e1434">

  Looking at this result, I can conclude that there is a causal relationship between the recommendation system and the number of subscribers of Disney+. In the end, I decided to use the casual estimate from the propensity score matching method. This is because the data is not randomized, I do not have access to the pre-treatment data, and not all the relationships we are analyzing are linear. Therefore, this indicates that having a recommendation system increases the number of Disney+ subscribers by 2,495. This result might be because the recommendation system successfully identifies subscribers’ preferences in shows. The subscribers are more likely to stay subscribed to Disney+ as a result of that. Because of this, the existing subscribers also might be more willing to recommend Disney+ to potential subscribers. 
  This could be important to Disney because now Disney+ can invest more in its recommendation system and perhaps recommendation system in other regions. 


# Conclusion/Recommendation: 
  Walt Disney Studios' mission “to entertain, inform, and inspire people around the globe through the power of unparalleled storytelling” left an imprint on every person’s childhood. Though making movies is a form of art, it is undeniable that every company is driven by bottom-line returns. Within this project, I attempted to find factors that impact Disney’s two highest revenue streams in hopes of helping the company dominate the market. In the end, I have reached the conclusion that Disney+ should continue the recommendation system and consider implementing a recommendation system in other regions. Since genres action, adventure, musical, romantic comedy, and western, thriller/suspense performed the best out of 12 genres in the “Disney Movies Total Gross” dataset, Disney should produce the majority of its movies under these 6 genres. With the heterogeneity test proving people under and above the age of 44 are indifferent toward villains/heroes, Disney can focus on promoting its movies to the population as a whole rather than targeting each segment differently. All of these techniques unveiled the result of Disney’s current production strategies and imply a potential for improvement using our findings. In the future, I may see Disney pivot to a more complex recommendation algorithm better suited for its global audience.



