# SchoolDistrict-Analysis
Challenge 4: Clean and Analyze District, School, and Student Data

### Overview of the School District Analysis
Remove testing data for a specific grade at a school, and analyze its effect across passing rates, funding buckets, types of schools, number of students, and other metrics/rankings.

### Results
After replaceing the data:
- District Summary: drop in both overall average math and reading scores, and overall pass rates (minimal)
- School Summary: drop in all metrics, but not enough to cause change in rankings
- School Type: drop in metrics, but matches pattern of Charter schools yielding higher pass rates and scores
- School Funding: drop in metrics, but Thomas High School still shows one of the best rates/scores for their respective funding group
- By Grade, and by School: the removed data had higher scores than the average school, but could reasonably fit in the dataset. Examining all the scores have shown that math has higher variance from school to school, and the scores are very influenced by which school the student goes to.

#### Affect on District Summary
Comparing District Summaries regarding the removal the compromised 9th grade results:
|                   | Total Schools | Total Students | Total Budget    | Average Math Score | Average Reading Score | % Passing Math | % Passing Reading | % Overall Passing |
|-------------------|---------------|----------------|-----------------|--------------------|-----------------------|----------------|-------------------|-------------------|
| Old (Not-Removed) | 15            | 39,170         | $24,649,428.00  |  79.0              | 81.9                  | 75.0           | 85.8              | 65.2              |
| New (Removed)     | 15            | 39,170         | $24,649,428.00  |  78.9              | 81.9                  | 74.8           | 85.7              | 64.9              |
| difference        | 0             | 0              | 0               | -0.1               | 0                     | -0.2           | -0.1              | -0.3              |

There was a drop in Average scores, but since this is at a district level, there is not much of an effect. The removal of 461 student results will not have a great effect on the total student count (39,170 students).


*Note that the only Scoring Data in the District Summary was altered, not the totals (eg: student counts, budget, etc.)*


Shown below, you can see how the revised count was calculated:

  Replacing 9th Grade Results Data is NaN:
  
        student_data_df.loc[(student_data_df["school_name"] == "Thomas High School") & (student_data_df["grade"] == "9th"),"math_score"] = np.nan

  Checking 9th Grade Results Data is NaN:
        
        # Check the student data for NaN's. 
        student_data_df.isnull().sum()
  
  Output:
        
        Student ID         0
        student_name       0
        gender             0
        grade              0
        school_name        0
        reading_score    461
        math_score       461
        dtype: int64
  
  
  Revising the student counts for passing percentage calculations:
  
        # These students have no grades. 
        students_to_remove = student_data_df.loc[(student_data_df["school_name"] == "Thomas High School") & (student_data_df["grade"]    == "9th"),"Student ID"].count()
        print("Students to Remove: ", students_to_remove)
        # Get the total student count 
        student_count = school_data_complete_df["Student ID"].count()
        print("Old Total Number of Students: ", student_count)

        # Step 2. Subtract the number of students that are in ninth grade at 
        # Thomas High School from the total student count to get the new total student count.
        new_student_count = student_count - students_to_remove
        print("New Total Number of Students: ", new_student_count)
  Output:

        Students to Remove:  461
        Old Total Number of Students:  39170
        New Total Number of Students:  38709





#### Affect on School Summary

Comparing School Summary (Thomas High School) regarding the removal the compromised 9th grade results:

Old School Summary:
        
        per_school_summary_df.loc["Thomas High School",:]
        
Output:
        
        School Type                  Charter
        Total Students                  1635
        Total School Budget      1.04313e+06
        Per Student Budget               638
        Average Math Score           83.3509
        Average Reading Score        83.8961
        % Passing Math               66.9113
        % Passing Reading            69.6636
        % Overall Passing            65.0765
        Name: Thomas High School, dtype: object
        
        
Updated School Summary:
        
        # Step 12. Replace the passing math percent for Thomas High School in the per_school_summary_df.
        per_school_summary_df.loc["Thomas High School","% Passing Math"] = non9th_passing_math_THS_percentage
        # Step 13. Replace the passing reading percentage for Thomas High School in the per_school_summary_df.
        per_school_summary_df.loc["Thomas High School","% Passing Reading"] = non9th_passing_read_THS_percentage
        # Step 14. Replace the overall passing percentage for Thomas High School in the per_school_summary_df.
        per_school_summary_df.loc["Thomas High School","% Overall Passing"] = non9th_passing_both_THS_percentage
        
        per_school_summary_df.loc["Thomas High School",:]
Output:
        
        School Type                                 Charter
        Total Students                                 1635
        Total School Budget                     1.04313e+06
        Per Student Budget                              638
        Average Math Score                          83.3509
        Average Reading Score                       83.8961
        % Passing Math                              93.1857
        % Passing Reading                           97.0187
        % Overall Passing                           90.6303
        Spending Ranges (Per Student)              $630-644
        School Size                      Medium (1000-2000)
        Name: Thomas High School, dtype: object
        
 Table Summary of changes:
 
|                   | School Type | Total Students | Total School Budget | Per Student Budget | Average Math Score | Average Reading Score | % Passing Math | % Passing Reading | % Overall Passing | Spending Ranges (Per Student) | School Size        |
|-------------------|-------------|----------------|---------------------|--------------------|--------------------|-----------------------|----------------|-------------------|-------------------|-------------------------------|--------------------|
| Old (Not Removed) | Charter     | 1635           |  $1,043,130.00      | 638                | 83.4183            | 83.0839               | 93.27%         | 97.31%            | 90.95%            | $630-644                      | Medium (1000-2000) |
| New (Removed)     | Charter     | 1635           |  $1,043,130.00      | 638                | 83.3509            | 83.8961               | 93.19%         | 97.02%            | 90.63%            | $630-644                      | Medium (1000-2000) |
| difference        |             |                |                     |                    |                    |                       | -0.09%         | -0.29%            | -0.32%            |                               |                    |


As shown by the table above, removing the scores had a minimal affect on the passing rates for Thomas High School.

#### Affect on Thomas High School (Rank versus Other Schools)
The affect on Thomas High School Ranking with other schools was minimally affected (Ranked by Overall Score):



| Rank | School                | School Type | Total Students | Total School Budget | Per Student Budget | Average Math Score | Average Reading Score | % Passing Math | % Passing Reading | % Overall Passing | Spending Ranges (Per Student) | School Size        |
|------|-----------------------|-------------|----------------|---------------------|--------------------|--------------------|-----------------------|----------------|-------------------|-------------------|-------------------------------|--------------------|
| 1    | Cabrera High School   | Charter     | 1858           | 1081356             | 582                | 83.061895          | 83.97578              | 94.133477      | 97.039828         | 91.334769         | <$584                         | Medium (1000-2000) |
| New:point_right:**2**    | **Thomas High School**    | **Charter**     | **1635**           | **1043130**             | **638**                | **83.350937**          | **83.896082**             | **93.18569**       | **97.018739**         | **90.630324**         | **$630-644**                      | **Medium (1000-2000)** |
| 3    | Griffin High School   | Charter     | 1468           | 917500              | 625                | 83.351499          | 83.816757             | 93.392371      | 97.138965         | 90.599455         | $585-629                      | Medium (1000-2000) |
| 4    | Wilson High School    | Charter     | 2283           | 1319574             | 578                | 83.274201          | 83.989488             | 93.867718      | 96.539641         | 90.582567         | <$584                         | Large (2000-5000)  |
| 5    | Pena High School      | Charter     | 962            | 585858              | 609                | 83.839917          | 84.044699             | 94.594595      | 95.945946         | 90.540541         | $585-629                      | Small (<1000)      |
| 6    | Wright High School    | Charter     | 1800           | 1049400             | 583                | 83.682222          | 83.955                | 93.333333      | 96.611111         | 90.333333         | <$584                         | Medium (1000-2000) |
| 7    | Shelton High School   | Charter     | 1761           | 1056600             | 600                | 83.359455          | 83.725724             | 93.867121      | 95.854628         | 89.892107         | $585-629                      | Medium (1000-2000) |
| 8    | Holden High School    | Charter     | 427            | 248087              | 581                | 83.803279          | 83.814988             | 92.505855      | 96.252927         | 89.227166         | <$584                         | Small (<1000)      |
| 9    | Bailey High School    | District    | 4976           | 3124928             | 628                | 77.048432          | 81.033963             | 66.680064      | 81.93328          | 54.642283         | $585-629                      | Large (2000-5000)  |
| 10   | Ford High School      | District    | 2739           | 1763916             | 644                | 77.102592          | 80.746258             | 68.309602      | 79.299014         | 54.289887         | $630-644                      | Large (2000-5000)  |
| 11   | Johnson High School   | District    | 4761           | 3094650             | 650                | 77.072464          | 80.966394             | 66.057551      | 81.222432         | 53.539172         | $645-675                      | Large (2000-5000)  |
| 12   | Hernandez High School | District    | 4635           | 3022020             | 652                | 77.289752          | 80.934412             | 66.752967      | 80.862999         | 53.527508         | $645-675                      | Large (2000-5000)  |
| 13   | Huang High School     | District    | 2917           | 1910635             | 655                | 76.629414          | 81.182722             | 65.683922      | 81.316421         | 53.513884         | $645-675                      | Large (2000-5000)  |
| 14   | Figueroa High School  | District    | 2949           | 1884411             | 639                | 76.711767          | 81.15802              | 65.988471      | 80.739234         | 53.204476         | $630-644                      | Large (2000-5000)  |
| 15   | Rodriguez High School | District    | 3999           | 2547363             | 637                | 76.842711          | 80.744686             | 66.366592      | 80.220055         | 52.988247         | $630-644                      | Large (2000-5000)  |

The result is that **Thomas High Schools jumps remains 2nd in ranking**.
As you will see from here on, these rankings across several categories where the removal of scores has the most dramatic effect. 

#### Affect on School Size/Type and Other Catagories
##### School Size
Thomas High School has 1635 Students, which classifies it as a "Medium" School.
Compared to other Medium Schools, the change made them go from Last/6th to 2nd.
Medium Schools tend to be have higher scores compared to the other categories, which could be attriubuted to having a good balance of budget to number of staff per student- providing each student with enough attention needed to learn (another topic/study should be made to confirm this).

Shown below are the average scores and passing rates of the schools and their respective sizes:

|   School Size       | Average Math Score  | Average Reading Score  | % Passing Math  | % Passing Reading  | % Overall Passing |
|---------------------|---------------------|------------------------|-----------------|--------------------|-------------------|
| Small (<1000)       | 83.8                | 83.9                   | 94              | 96                 | 90                |
| :point_right:Medium (1000-2000)  | 83.4                | 83.9                   | 94              | 97                 | 91                |
| Large (2000-5000)   | 77.7                | 81.3                   | 70              | 83                 | 58                |

The code used to curate this table is as follows:
        
        # Assemble into DataFrame. 
        size_summary_df = pd.DataFrame({
            "Average Math Score" : size_math_scores,
            "Average Reading Score": size_reading_scores,
            "% Passing Math": size_passing_math,
            "% Passing Reading": size_passing_reading,
            "% Overall Passing": size_overall_passing})
        size_summary_df
        
Extra formatting was conducted to make the table more readable.

Without removing the 9th grade data, the Thomas High School would rank last of the medium sized schools.

##### School Type
Thomas High School is classified as a "Charter" School.
Compared to other Charter Schools, the change made them remain in 2nd.
Charter Schools tend to be have higher scores compared to the "District" schools despite being similar to District Schools in Budget per student. This could be attributed to having to pay for school increasing the likelyhood of a student having the support structure needed to complete school, as apposed to the District/Public Schools, where there may be a wider range of income/access to resources for a student to succeed.

Shown below are the average scores and passing rates of the charter schools:


| Rank | School                | School Type | Total Students | Total School Budget | Per Student Budget | Average Math Score | Average Reading Score | % Passing Math | % Passing Reading | % Overall Passing | Spending Ranges (Per Student) | School Size        |
|------|-----------------------|-------------|----------------|---------------------|--------------------|--------------------|-----------------------|----------------|-------------------|-------------------|-------------------------------|--------------------|
| 1    | Cabrera High School   | Charter     | 1858           | 1081356             | 582                | 83.061895          | 83.97578              | 94.133477      | 97.039828         | 91.334769         | <$584                         | Medium (1000-2000) |
| New:point_right:**2**    | **Thomas High School**    | **Charter**     | **1635**           | **1043130**             | **638**                | **83.350937**          | **83.896082**             | **93.18569**       | **97.018739**         | **90.630324**         | **$630-644**                      | **Medium (1000-2000)** |
| 3    | Griffin High School   | Charter     | 1468           | 917500              | 625                | 83.351499          | 83.816757             | 93.392371      | 97.138965         | 90.599455         | $585-629                      | Medium (1000-2000) |
| 4    | Wilson High School    | Charter     | 2283           | 1319574             | 578                | 83.274201          | 83.989488             | 93.867718      | 96.539641         | 90.582567         | <$584                         | Large (2000-5000)  |
| 5    | Pena High School      | Charter     | 962            | 585858              | 609                | 83.839917          | 84.044699             | 94.594595      | 95.945946         | 90.540541         | $585-629                      | Small (<1000)      |
| 6    | Wright High School    | Charter     | 1800           | 1049400             | 583                | 83.682222          | 83.955                | 93.333333      | 96.611111         | 90.333333         | <$584                         | Medium (1000-2000) |
| 7    | Shelton High School   | Charter     | 1761           | 1056600             | 600                | 83.359455          | 83.725724             | 93.867121      | 95.854628         | 89.892107         | $585-629                      | Medium (1000-2000) |
| 8    | Holden High School    | Charter     | 427            | 248087              | 581                | 83.803279          | 83.814988             | 92.505855      | 96.252927         | 89.227166         | <$584                         | Small (<1000)      |

Here is a comparison of the charter schools to public schools:

| School Type | Average Math Score | Average Reading Score | % Passing Math | % Passing Reading | % Overall Passing |
|-------------|--------------------|-----------------------|----------------|-------------------|-------------------|
| :point_right:Charter     | 83.5               | 83.9                  | 94             | 97                | 90                |
| District    | 77                 | 81                    | 67             | 81                | 54                |
| difference  | -6.5               | -2.9                  | -27            | -16               | -36               |


The code used to curate this table is as follows:

    # Calculate averages for the desired columns. 
    type_math_scores = per_school_summary_df.groupby(["School Type"]).mean()["Average Math Score"]
    type_reading_scores = per_school_summary_df.groupby(["School Type"]).mean()["Average Reading Score"]
    type_passing_math = per_school_summary_df.groupby(["School Type"]).mean()["% Passing Math"]
    type_passing_reading = per_school_summary_df.groupby(["School Type"]).mean()["% Passing Reading"]
    type_overall_passing = per_school_summary_df.groupby(["School Type"]).mean()["% Overall Passing"]
    
    # Assemble into DataFrame. 
    type_summary_df = pd.DataFrame({
        "Average Math Score" : type_math_scores,
        "Average Reading Score": type_reading_scores,
        "% Passing Math": type_passing_math,
        "% Passing Reading": type_passing_reading,
        "% Overall Passing": type_overall_passing})
    type_summary_df
        
Extra formatting was conducted to make the table more readable.

##### School Spending
Thomas High School has 1635 Students and a budget of $1,056,600, which rolls out to $638 per student.
When breaking out the spending per student into 4 groups:

- <$584
- $585-629
- $630-644
- $645-675

Thomas High School falls in the **$630-644** group, which sees a significantly lower overall score compared to Thomas High School. The Group Overall Score is 63, which is likely being brought up by Thomas High School's both old and new overall score.

| Spending (Per Student) | Average Math Score | Average Reading Score | % Passing Math | % Passing Reading | % Overall Passing |
|------------------------|--------------------|-----------------------|----------------|-------------------|-------------------|
| <$584                  | 83.5               | 83.9                  | 93             | 97                | 90                |
| $585-629               | 81.9               | 83.2                  | 87             | 93                | 81                |
| :point_right:$630-644               | 78.5               | 81.6                  | 73             | 84                | :point_right:63                |
| $645-675               | 77                 | 81                    | 66             | 81                | 54                |

The code used to curate this table is as follows:
        
        # Establish the spending bins and group names.
        spending_bins = [0, 585, 630, 645, 675]
        group_names = ["<$584", "$585-629", "$630-644", "$645-675"]

        # Categorize spending based on the bins.
        per_school_summary_df["Spending Ranges (Per Student)"] = pd.cut(per_school_capita, spending_bins, labels=group_names)

        # Calculate averages for the desired columns. 
        spending_math_scores = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["Average Math Score"]
        spending_reading_scores = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["Average Reading Score"]
        spending_passing_math = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Passing Math"]
        spending_passing_reading = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Passing Reading"]
        overall_passing_spending = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Overall Passing"]

        # Create the DataFrame
        spending_summary_df = pd.DataFrame({
            "Average Math Score" : spending_math_scores,
            "Average Reading Score": spending_reading_scores,
            "% Passing Math": spending_passing_math,
            "% Passing Reading": spending_passing_reading,
            "% Overall Passing": overall_passing_spending})
        spending_summary_df

Extra formatting was conducted to make the table more readable.

Without removing the 9th grade data, the $630-644 group would rank last of the spending per student groups.

#### Math and Reading Scores by Grade
Without removing the 9th grade scores, Thomas High School had 461 Students, where:

- 431 Students Passed Math    (93.49% Pass Rate, 83.59 Average Score)
- 452 Students Passed Reading (98.05% Pass Rate, 83.73 Average Score)
- 423 Students Passed Both    (90.95% Pass Rate)
- Average overall grade was 83.66%

This fits the overall grade trend when comparing schools across different grade classes:

**Reading Scores**
| School                | 9th  | 10th | 11th | 12th |
|-----------------------|------|------|------|------|
| Bailey High School    | 81.3 | 80.9 | 80.9 | 80.9 |
| Cabrera High School   | 83.7 | 84.3 | 83.8 | 84.3 |
| Figueroa High School  | 81.2 | 81.4 | 80.6 | 81.4 |
| Ford High School      | 80.6 | 81.3 | 80.4 | 80.7 |
| Griffin High School   | 83.4 | 83.7 | 84.3 | 84.0 |
| Hernandez High School | 80.9 | 80.7 | 81.4 | 80.9 |
| Holden High School    | 83.7 | 83.3 | 83.8 | 84.7 |
| Huang High School     | 81.3 | 81.5 | 81.4 | 80.3 |
| Johnson High School   | 81.3 | 80.8 | 80.6 | 81.2 |
| Pena High School      | 83.8 | 83.6 | 84.3 | 84.6 |
| Rodriguez High School | 81.0 | 80.6 | 80.9 | 80.4 |
| Shelton High School   | 84.1 | 83.4 | 84.4 | 82.8 |
| :point_right:Thomas High School    | :point_right:nan  | 84.3 | 83.6 | 83.8 |
| Wilson High School    | 83.9 | 84.0 | 83.8 | 84.3 |
| Wright High School    | 83.8 | 83.8 | 84.2 | 84.1 |
| Average               | 82.4 | 82.5 | 82.6 | 82.6 |

The data removed from the 9th grade class would not be an outlier if kept.
For ease of visualization, here is a heatmap of the grades:


![image](https://user-images.githubusercontent.com/80546200/113532215-7246bd80-9590-11eb-8489-0984a315ee28.png)


As you can see, a 83.73 would fit in this pattern without causing many issues.

**Math Scores**
| School                | 9th  | 10th | 11th | 12th |
|-----------------------|------|------|------|------|
| Bailey High School    | 77.1 | 77.0 | 77.5 | 76.5 |
| Cabrera High School   | 83.1 | 83.2 | 82.8 | 83.3 |
| Figueroa High School  | 76.4 | 76.5 | 76.9 | 77.2 |
| Ford High School      | 77.4 | 77.7 | 76.9 | 76.2 |
| Griffin High School   | 82.0 | 84.2 | 83.8 | 83.4 |
| Hernandez High School | 77.4 | 77.3 | 77.1 | 77.2 |
| Holden High School    | 83.8 | 83.4 | 85.0 | 82.9 |
| Huang High School     | 77.0 | 75.9 | 76.4 | 77.2 |
| Johnson High School   | 77.2 | 76.7 | 77.5 | 76.9 |
| Pena High School      | 83.6 | 83.4 | 84.3 | 84.1 |
| Rodriguez High School | 76.9 | 76.6 | 76.4 | 77.7 |
| Shelton High School   | 83.4 | 82.9 | 83.4 | 83.8 |
| :point_right:Thomas High School    | :point_right:nan  | 83.1 | 83.5 | 83.5 |
| Wilson High School    | 83.1 | 83.7 | 83.2 | 83.0 |
| Wright High School    | 83.3 | 84.0 | 83.8 | 83.6 |
| Average               | 80.1 | 80.4 | 80.6 | 80.4 |

Although an 83.59 would not be an outlier, it would not fit the data as well as the reading score, as the math scores acros the grade levels are lower.
For ease of visualization, here is a heatmap of the grades:


![image](https://user-images.githubusercontent.com/80546200/113532733-b38b9d00-9591-11eb-931e-9dcfe2b16b24.png)


There is greater disparity of grades amongst the schools than there is among the grade levels.

The code used to curate this table is as follows:

        # Create a Series of scores by grade levels using conditionals.

        ninth_graders = school_data_complete_df[(school_data_complete_df["grade"] == "9th")]
        tenth_graders = school_data_complete_df[(school_data_complete_df["grade"] == "10th")]
        eleventh_graders = school_data_complete_df[(school_data_complete_df["grade"] == "11th")]
        twelfth_graders = school_data_complete_df[(school_data_complete_df["grade"] == "12th")]

        # Group each school Series by the school name for the average math score.

        ninth_grade_math_scores = ninth_graders.groupby(["school_name"]).mean()["math_score"]
        tenth_grade_math_scores = tenth_graders.groupby(["school_name"]).mean()["math_score"]
        eleventh_grade_math_scores = eleventh_graders.groupby(["school_name"]).mean()["math_score"]
        twelfth_grade_math_scores = twelfth_graders.groupby(["school_name"]).mean()["math_score"]

        # Group each school Series by the school name for the average reading score.

        ninth_grade_reading_scores = ninth_graders.groupby(["school_name"]).mean()["reading_score"]
        tenth_grade_reading_scores = tenth_graders.groupby(["school_name"]).mean()["reading_score"]
        eleventh_grade_reading_scores = eleventh_graders.groupby(["school_name"]).mean()["reading_score"]
        twelfth_grade_reading_scores = twelfth_graders.groupby(["school_name"]).mean()["reading_score"]


        # Create a Series of scores by grade levels using conditionals.

        ninth_graders = school_data_complete_df[(school_data_complete_df["grade"] == "9th")]
        tenth_graders = school_data_complete_df[(school_data_complete_df["grade"] == "10th")]
        eleventh_graders = school_data_complete_df[(school_data_complete_df["grade"] == "11th")]
        twelfth_graders = school_data_complete_df[(school_data_complete_df["grade"] == "12th")]

        # Group each school Series by the school name for the average math score.

        ninth_grade_math_scores = ninth_graders.groupby(["school_name"]).mean()["math_score"]
        tenth_grade_math_scores = tenth_graders.groupby(["school_name"]).mean()["math_score"]
        eleventh_grade_math_scores = eleventh_graders.groupby(["school_name"]).mean()["math_score"]
        twelfth_grade_math_scores = twelfth_graders.groupby(["school_name"]).mean()["math_score"]

        # Group each school Series by the school name for the average reading score.

        ninth_grade_reading_scores = ninth_graders.groupby(["school_name"]).mean()["reading_score"]
        tenth_grade_reading_scores = tenth_graders.groupby(["school_name"]).mean()["reading_score"]
        eleventh_grade_reading_scores = eleventh_graders.groupby(["school_name"]).mean()["reading_score"]
        twelfth_grade_reading_scores = twelfth_graders.groupby(["school_name"]).mean()["reading_score"]

        # Combine each Series for average math scores by school into single data frame.
        math_scores_by_grade = pd.DataFrame({
                "9th": ninth_grade_math_scores,
                "10th": tenth_grade_math_scores,
                "11th": eleventh_grade_math_scores,
                "12th": twelfth_grade_math_scores})

        math_scores_by_grade
        # Combine each Series for average reading scores by school into single data frame.
        reading_scores_by_grade = pd.DataFrame({
                      "9th": ninth_grade_reading_scores,
                      "10th": tenth_grade_reading_scores,
                      "11th": eleventh_grade_reading_scores,
                      "12th": twelfth_grade_reading_scores})
        reading_scores_by_grade

Extra formatting was conducted to make the table more readable.

### Summary
After replacing the data, the areas of interest that are affected:
- Average Math Scores for Thomas High School were higher than that of other 9th Graders
- Average Reading Scores for Thomas High School were higher than that of other 9th Graders
- The overall pass rate dropped for Thomas High School, but its rankings remain unaffected
- The overall scores for both reading and writing for the disctrict go down

