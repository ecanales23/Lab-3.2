	<nav> 
        <h3><a href="../projects/index.html" class="current"> Return to Projects</a></h3>

    </nav> 
	<header>
		<h1>
			<small>Assignment 2</small>
			Exploratory Data Analysis
		</h1>

		<p>
			<strong>Emily Canales</strong> — <em>eycanal@mit.edu</em>
		</p>
	</header>
	<main>
		<section>
			<h2>Subtheme: Corporate Landlords & Evictions</h2>

			<h3>Overall Analysis Questions</h3>
			<ol>
				<li>How much of Boston's housing is owned by major corporations? <br>
				I wanted to first ask this question because this is what the dataset is primarily showing and also to get a feel for how much of Boston’s housing is owned by large corporations. As the reading Housing Justice Beyond the Emergency (Huntley et al., 2022) mentioned, corporate landowners tend to have higher eviction rates.</li>
				<li>How has corporate ownership changed over time?</li>
				<li>What are the most corporate owned neighborhoods in Boston? Which are the least?</li>
				<li>Is corporate property more likely to be vacant?
					<br> As said by the readings evictions are a major issue across the US and also in Boston so this question is to tie vacancy rates to corporate ownership and reveal trends.
				</li>
				<li>What are the demographics like in these highly vacant areas?
					<br>As stated in the readings, Black, Brown, and Indigenous renters are more likely to be evicted than white renters so I wanted to see what the demographics differ in high corporate owned areas and low.

				</li>
				
				
			</ol>
		</section>

		<section>
			<h2>Discoveries & Insights</h2>
			<p>This dataset connects corporate ownership to neighborhood boundaries, vacancy rates, and census data including but not limited to renter race, ethnicity, and age. We begin this investigation by tying neighborhoods and the percent of home corporate ownership. My first step was to compare the average corporate ownership rate in all of Boston from 2004-2024. I began by checking for null values and the number of data points for each year. There were no null values in corporate ownership rate and 21 points consistently through 2004-2024 as shown below.
</p>


			<figure>
				<img src="Fig1.png" />
				<figcaption>
				This chart simply shows that there exists 21 data points for each year so when we take the average corporate ownership and plot it against year below, we know the averages have been calculated equally and should not have data quality issues in terms of corporate owner rates. 

				</figcaption>
			</figure>

			<figure>
				<img src="A2 images/Fig2.png" />
				<figcaption>
				I then repeated the same for owner-occupancy rates. 
				</figcaption>
			</figure>

			<figure>
				<img src="A2 images/Fig3.png">
				<figcaption>
				Reflected above is this previously mentioned map that shows the average corporate ownership rate over time. It also shows owner-occupancy rates over time and its decline. This gives us a sense of how corporate ownership has changed from 2004 till now and as we can see there has been large increases. Particularly from 2016-2024 where the changes become more steep. 
				</figcaption>
			</figure>

			<figure>
				<img src="A2 images/Fig4.png" />
				<figcaption>
				Next, I wanted to see how corporate ownership varies by neighborhood. The graph above shows the average corporate ownership for Boston neighborhoods from 2004-2024 descending from highest to lowest. For reference, South Boston is split into “South Boston” and “South Boston - Waterfront” . I decided not to merge these two categories because waterfront property tends to be much more expensive and the difference between corporate ownership rate is quite notable. In fact, “South Boston - Waterfront” has the second highest corporate ownership rate, second only to Fenway. Meanwhile, “South Boston” falls under the lower half. 
				</figcaption>
			</figure>

			<figure>
				<img src="A2 images/Fig5.png" />
				<figcaption>
				I then wanted to zone in on the years where corporate ownership started to take off. Therefore I further filtered the graph above to show data from 2016-2024 and for the following graphs I will continue to zoom in on these years. After the change, we see many of the averages rise to higher numbers and “South-Boston Waterfront” overtake Fenway.
				</figcaption>
			</figure>

			<figure>
				<img src="A2 images/Fig6.png" />
				<figcaption>
				After viewing the previous graph I was curious to compare vacancy rates, corporate ownership rates, and owner occupancy rates by neighborhoods. I wanted to see the composition of ownership particularly in the neighborhoods that have vacancy rates. I calculated the vacancy rate value with [vacant units]/[total units]. I believe the findings to be quite interesting since neighborhoods with very high owner occupancy rates such as West Roxbury, Mattapan, Jamaica Plain, etc. have low vacancy rates. As we saw in the previous graph South-Boston Waterfront and Downtown have very high corporate ownership and also have high vacancy rates. Leaving the question if corporate-owned housing is tied to increased vacancies. I cannot say for certain that corporate housing causes increased vacancies, but there does seem to be a correlation.

				</figcaption>
			</figure>

			<figure>
				<img src="A2 images/Fig7.png" />
				<figcaption>
				Diving deeper into this issue, I decided to take a more focused look on the neighborhoods of West Roxbury, Mattapan, South-Boston Waterfront, and Downtown. Above are the ethnic compositions of these neighborhoods. The size of the pie charts are relative to the total population, while the colors dictate race composition. Almost all of the neighborhoods are primarily white except for Mattapan which is a historically black neighborhood. 
				</figcaption>
			</figure>

			<figure>
				<img src="A2 images/Fig8.png" />
				<figcaption>
				Next, I wanted to measure how the composition has changed over time in these neighborhoods. However, I ran into a major data issue where the census data population does not change over the years. I looked into it more and all the census data shows the same values per year, only the owner occupancy rate and corporate ownership rate vary. This is most likely due to a joins error where the year was not considered and census data simply joined by neighborhood name. Therefore, I could not dive deeper into how vacancy rates and demographics have changed over time with this dataset alone. It would be very telling if demographic populations changed with corporate ownership. 
				</figcaption>
			</figure>

		
		</section>

		<section>
			<h2>Summary</h2>

			<p>In conclusion, the corporate ownership and vacancy rates vary greatly across different parts of Boston.
				In this analysis we explored potential correlations with corporate housing such as vacancy rates and demographic composition.
				Vacancy rates and corporate ownership seem to be linked mostly at the high and low ends of the values. Meanwhile more moderate values have less of a correlation.
				From the select neighborhoods there does not seem to be an apparent link between corporate ownership and select demographics. However,
				this does not mean there is no link whatsoever. The data was not available for other years so it is possible that there were changes in demographics and vacancy rates overtime that were not shown.
			</p>
		</section>
	</main>