	-- tab interactions for users less than 1 year--
SELECT 
	user_id,
	event_code,
	s.q1 AS years_usage_q1,
	ceiling(AVG(LEFT(data1,1)::integer)) AS avg_windows_opened,
	ceiling(AVG(LEFT(data2, 1)::integer)) AS avg_tabs_opened
FROM 
	survey s
LEFT JOIN events e USING(user_id)
WHERE event_code = 26 AND s.q1::Integer<=2
GROUP BY 1,2,3;

-- tab interactions for users more than 1 year--
SELECT 
	user_id,
	s.q1 AS years_usage_q1,
	event_code,
	ceiling(AVG(LEFT(data1,1)::integer)) AS avg_windows_opened,
	ceiling(AVG(LEFT(data2, 1)::integer)) AS avg_tabs_opened
FROM 
	survey s
LEFT JOIN events e USING(user_id) 
WHERE event_code = 26 AND s.q1::Integer>=3
GROUP BY 1,2,3;

-- bookmark interactions for users less than 1 year--
SELECT 
	user_id,
	s.q1 AS user_less_than_1year,
	COUNT(event_code) AS Interaction_bookmark
FROM 
	events e
LEFT JOIN survey s USING(user_id)
WHERE (event_code >=8 AND event_code <=11)  AND s.q1::Integer<=2
GROUP BY 1,2;


-- bookmark interactions for users more than 1 year--
SELECT 
	user_id,
	s.q1 AS user_less_than_1year,
	COUNT(event_code) AS Interaction_bookmark
    --TIMESTAMP 'epoch' AS epoch,
    --"timestamp" * INTERVAL '1 Millisecond ' AS duration,
    --(TIMESTAMP 'epoch' + "timestamp" * INTERVAL '1 Millisecond ') AS readable_timestamp 
	-- ceiling(AVG(LEFT(data1,1)::integer)) AS avg_windows_opened,
	-- ceiling(AVG(LEFT(data2, 1)::integer)) AS avg_tabs_opened
FROM 
	events e
LEFT JOIN survey s USING(user_id)
WHERE (event_code >=8 AND event_code <=11)  AND s.q1::Integer>2
GROUP BY 1,2;

				-- EXPLORE DATA --

-- How many users in events filled in the survey?
Select COUNT(DISTINCT user_id) AS total_event_users FROM events;

SELECT COUNT(DISTINCT user_ID) AS total_survey_users FROM survey;


-- How many users in survey are male to female?
SELECT 
	CASE WHEN q5::INT = 0 THEN 'male'
		WHEN q5::INT = 1 THEN 'female'
	END AS male_to_female,
	COUNT(DISTINCT user_id) AS user_count
FROM survey
GROUP BY 1;

-- What are the age differences for users in survey
SELECT 
	CASE WHEN q6::INT = 0 THEN '0: Under 18'
		WHEN q6::INT = 1 THEN '1: 18-25'
		WHEN q6::INT = 2 THEN '2: 26-35'
		WHEN q6::INT = 3 THEN '3: 36-45'
		WHEN q6::INT = 4 THEN '4: 46-55'
		WHEN q6::INT = 5 THEN '5: Older than 55'
	END AS age_segment,
	COUNT(DISTINCT user_id) AS user_count
FROM survey
GROUP BY 1;

-- How much time do each survey users spend on the web?
SELECT 
	CASE WHEN q7::INT = 0 THEN '0: Less than 1 hour'
		WHEN q7::INT = 1 THEN '1: 1-2 hours'
		WHEN q7::INT = 2 THEN '2: 2-4 hours'
		WHEN q7::INT = 3 THEN '3: 4-6 hours'
		WHEN q7::INT = 4 THEN '4: 6-8 hours'
		WHEN q7::INT = 5 THEN '5: 8-10 hours'
		WHEN q7::INT = 6 THEN '6: More than 10 hours'
	END AS Time_Spend_Online,
	COUNT(DISTINCT user_id) AS user_count
FROM survey
GROUP BY 1;

-- What are the web user skills segment?
SELECT 
	CASE WHEN q8::INT = 1 THEN '1/10'
		WHEN q8::INT = 2 THEN '2/10'
		WHEN q8::INT = 3 THEN '3/10'
		WHEN q8::INT = 4 THEN '4/10'
		WHEN q8::INT = 5 THEN '5/10'
		WHEN q8::INT = 6 THEN '6/10'
		WHEN q8::INT = 7 THEN '7/10'
		WHEN q8::INT = 8 THEN '8/10'
		WHEN q8::INT = 9 THEN '9/10'
		WHEN q8::INT = 10 THEN '10/10'
	END AS Self_Rate_Computer_Web_Skills,
	COUNT(DISTINCT user_id) AS user_count
FROM survey
GROUP BY 1;

-- Where do survey users access the internet --

SELECT 
    SUM(CASE WHEN q9 LIKE '%0%' THEN 1 ELSE 0 END) AS home,
    SUM(CASE WHEN q9 LIKE '%1%' THEN 1 ELSE 0 END) AS work,
    SUM(CASE WHEN q9 LIKE '%2%' THEN 1 ELSE 0 END) AS school,
    SUM(CASE WHEN q9 LIKE '%3%' THEN 1 ELSE 0 END) AS Mobile,
    SUM(CASE WHEN q9 IS NULL THEN 1 ELSE 0 END) AS null,
    COUNT(*) AS user_count
FROM survey;


-- Reason for survey users to use the internet --

SELECT 
    SUM(CASE WHEN q11 LIKE '%0%' THEN 1 ELSE 0 END) AS Work_coding_related,
    SUM(CASE WHEN q11 LIKE '%1%' THEN 1 ELSE 0 END) AS Work_noncoding_related,
    SUM(CASE WHEN q11 LIKE '%2%' THEN 1 ELSE 0 END) AS School,
    SUM(CASE WHEN q11 LIKE '%3%' THEN 1 ELSE 0 END) AS Personal_life_assistance,
    SUM(CASE WHEN q11 LIKE '%4%' THEN 1 ELSE 0 END) AS Communication,
    SUM(CASE WHEN q11 LIKE '%5%' THEN 1 ELSE 0 END) AS Socializing,
    SUM(CASE WHEN q11 LIKE '%6%' THEN 1 ELSE 0 END) AS Entertainment,
    SUM(CASE WHEN q11 IS NULL THEN 1 ELSE 0 END) AS null,
    COUNT(*) AS user_count
FROM survey;

-- Most frequently visited Websites --
SELECT 
    SUM(CASE WHEN q12 LIKE '%0%' THEN 1 ELSE 0 END) AS Search_engines,
    SUM(CASE WHEN q12 LIKE '%1%' THEN 1 ELSE 0 END) AS Video_sites,
    SUM(CASE WHEN q12 LIKE '%2%' THEN 1 ELSE 0 END) AS News_sites,
    SUM(CASE WHEN q12 LIKE '%3%' THEN 1 ELSE 0 END) AS Social_networking_sites,
    SUM(CASE WHEN q12 LIKE '%4%' THEN 1 ELSE 0 END) AS Browser_based_games,
    SUM(CASE WHEN q12 LIKE '%5%' THEN 1 ELSE 0 END) AS Shopping,
    SUM(CASE WHEN q12 LIKE '%6%' THEN 1 ELSE 0 END) AS online_banking,
    SUM(CASE WHEN q12 LIKE '%7%' THEN 1 ELSE 0 END) AS File_download ,
    SUM(CASE WHEN q12 LIKE '%8%' THEN 1 ELSE 0 END) AS Webmail,
    SUM(CASE WHEN q12 LIKE '%9%' THEN 1 ELSE 0 END) AS Forums,
    SUM(CASE WHEN q12 LIKE '%10%' THEN 1 ELSE 0 END) AS Adult_pages,
    SUM(CASE WHEN q12 LIKE '%11%' THEN 1 ELSE 0 END) AS Gambling ,
    SUM(CASE WHEN q12 LIKE '%12%' THEN 1 ELSE 0 END) AS Online_Word_Proccessing,
    SUM(CASE WHEN q12 IS NULL THEN 1 ELSE 0 END) AS null,
    COUNT(*) AS user_count
FROM survey;

					-- Tabs --
-- Total female that interacted with tabs--
SELECT
	COUNT(s.user_id)
FROM 
	survey s
WHERE s.q5::Integer=0 AND(s.q6::integer=1 OR s.q6::integer=2 OR s.q6::integer=3) AND (s.q8::integer>=8);

--How many tabs do target female users open? --
	--By Median--
SELECT 
	user_id,
	event_code,
	s.q5 AS Female_User,
	s.q6 AS AGE,
	s.q8 AS Skills,
	Median(SPLIT_PART(data2, ' ',1)::INTEGER) AS median_tabs_opened
FROM 
	survey s
FULL JOIN events e USING(user_id)
WHERE event_code = 26 AND s.q5::Integer=1 AND(s.q6::integer=1 OR s.q6::integer=2 OR s.q6::integer=3) AND (s.q8::integer>=8)
GROUP BY 1,2,3,4,5;

	--By Max--
SELECT 
	user_id,
	event_code,
	s.q5 AS Female_User,
	s.q6 AS AGE,
	s.q8 AS Skills,
	MAX(SPLIT_PART(data2, ' ',1)::INTEGER) AS max_tabs_opened
FROM 
	survey s
FULL JOIN events e USING(user_id)
WHERE event_code = 26 AND s.q5::Integer=1 AND(s.q6::integer=1 OR s.q6::integer=2 OR s.q6::integer=3) AND (s.q8::integer>=8)
GROUP BY 1,2,3,4,5;


	--By AVG--
SELECT 
	user_id,
	event_code,
	s.q5 AS Female_User,
	s.q6 AS AGE,
	s.q8 AS Skills,
	AVG(SPLIT_PART(data2, ' ',1)::INTEGER) AS avg_tabs_opened
FROM 
	survey s
FULL JOIN events e USING(user_id)
WHERE event_code = 26 AND s.q5::Integer=1 AND(s.q6::integer=1 OR s.q6::integer=2 OR s.q6::integer=3) AND (s.q8::integer>=8)
GROUP BY 1,2,3,4,5;


--Compare to Male Users-- 
	--By Median--
SELECT 
	user_id,
	event_code,
	s.q5 AS male_User,
	s.q6 AS AGE,
	s.q8 AS Skills,
	Median(SPLIT_PART(data1, ' ',1)::INTEGER) AS median_windows_opened,
	Median(SPLIT_PART(data2, ' ',1)::INTEGER) AS median_tabs_opened
FROM 
	survey s
FULL JOIN events e USING(user_id)
WHERE event_code = 26 AND s.q5::Integer=0 AND(s.q6::integer=1 OR s.q6::integer=2 OR s.q6::integer=3) AND (s.q8::integer>=8)
GROUP BY 1,2,3,4,5;

	--By Max--
SELECT 
	user_id,
	event_code,
	s.q5 AS male_User,
	s.q6 AS AGE,
	s.q8 AS Skills,
	MAX(SPLIT_PART(data1, ' ',1)::INTEGER) AS max_windows_opened,
	MAX(SPLIT_PART(data2, ' ',1)::INTEGER) AS max_tabs_opened
FROM 
	survey s
FULL JOIN events e USING(user_id)
WHERE event_code = 26 AND s.q5::Integer=0 AND(s.q6::integer=1 OR s.q6::integer=2 OR s.q6::integer=3) AND (s.q8::integer>=8)
GROUP BY 1,2,3,4,5;

	--By AVG--
SELECT 
	user_id,
	event_code,
	s.q5 AS male_User,
	s.q6 AS AGE,
	s.q8 AS Skills,
	AVG(SPLIT_PART(data2, ' ',1)::INTEGER) AS avg_tabs_opened
FROM 
	survey s
FULL JOIN events e USING(user_id)
WHERE event_code = 26 AND s.q5::Integer=0 AND(s.q6::integer=1 OR s.q6::integer=2 OR s.q6::integer=3) AND (s.q8::integer>=8)
GROUP BY 1,2,3,4,5;


					-- Bookmarks(target female user) --

--How many BOOKMARKS do target female users HAVE? --
SELECT 
	user_id,
	event_code,
	s.q5 AS Female_User,
	s.q6 AS AGE,
	s.q8 AS Skills,
	(SPLIT_PART(data1, ' ',1)::INTEGER) AS total_bookmarks,
	(SPLIT_PART(data2, ' ',1)::INTEGER) AS total_bookmark_folders,
	(SPLIT_PART(data3, ' ',3)::INTEGER) AS  maximum_depth_bookmark_folders
FROM 
	survey s
FULL JOIN events e USING(user_id)
WHERE event_code = 8 AND s.q5::Integer=1 AND(s.q6::integer=1 OR s.q6::integer=2 OR s.q6::integer=3) AND (s.q8::integer>=8)
GROUP BY 1,2,3,4,5,6,7,8;

-- Backcheck if Bookmark event 8 changes--
SELECT 
	user_id,
	event_code,
	data1
FROM events
WHERE event_code = 8;

--How many BOOKMARKS created do target female users HAVE that day? --
SELECT 
	user_id,
	event_code,
	s.q5 AS Female_User,
	s.q6 AS AGE,
	s.q8 AS Skills,
	COUNT(data1) AS total_times_created_bookmark
FROM 
	survey s
FULL OUTER JOIN events e USING(user_id)
WHERE event_code = 9 AND s.q5::Integer=1 AND(s.q6::integer=1 OR s.q6::integer=2 OR s.q6::integer=3) AND (s.q8::integer>=8)
GROUP BY 1,2,3,4,5;
--Only 5 female users created new bookmark that day

--How many times a target female users Choose a bookmark that day? --
SELECT 
	user_id,
	Count(event_code) AS bookmark_chosen,
	s.q5 AS Female_User,
	s.q6 AS AGE,
	s.q8 AS Skills
FROM 
	survey s
FULL JOIN events e USING(user_id)
WHERE event_code = 10 AND s.q5::Integer=1 AND(s.q6::integer=1 OR s.q6::integer=2 OR s.q6::integer=3) AND (s.q8::integer>=8)
GROUP BY 1,3,4,5;

--How many BOOKMARKS modified do target female users HAVE that day? --
SELECT 
	user_id,
	event_code,
	s.q5 AS Female_User,
	s.q6 AS AGE,
	s.q8 AS Skills,
	COUNT(data1) AS number_of_bookmark_modified
FROM 
	survey s
FULL JOIN events e USING(user_id)
WHERE event_code = 11 AND s.q5::Integer=1 AND(s.q6::integer=1 OR s.q6::integer=2 OR s.q6::integer=3) AND (s.q8::integer>=8)
GROUP BY 1,2,3,4,5;
--only 2 users modified that day

-- total female users:

SELECT 
	COUNT(DISTINCT user_id)
FROM 
	survey
WHERE q5::Integer=1;
-- total of 257 Female users

--What Web Activities do Target female users do?
SELECT 
	COUNT(user_id) AS Number_of_target_female_user,
	SUM(CASE WHEN q11 LIKE '%0%' THEN 1 ELSE 0 END) AS Work_coding_related,
    SUM(CASE WHEN q11 LIKE '%1%' THEN 1 ELSE 0 END) AS Work_noncoding_related,
    SUM(CASE WHEN q11 LIKE '%2%' THEN 1 ELSE 0 END) AS School,
    SUM(CASE WHEN q11 LIKE '%3%' THEN 1 ELSE 0 END) AS Personal_life_assistance,
    SUM(CASE WHEN q11 LIKE '%4%' THEN 1 ELSE 0 END) AS Communication,
    SUM(CASE WHEN q11 LIKE '%5%' THEN 1 ELSE 0 END) AS Socializing,
    SUM(CASE WHEN q11 LIKE '%6%' THEN 1 ELSE 0 END) AS Entertainment,
    SUM(CASE WHEN q11 IS NULL THEN 1 ELSE 0 END) AS null
FROM 
	survey s
WHERE s.q5::Integer=1 AND(s.q6::integer=1 OR s.q6::integer=2 OR s.q6::integer=3) AND (s.q8::integer>=8);

-- What Websites do Target female users surf?
SELECT 
	COUNT(user_id) AS Number_of_target_female_user,
 	SUM(CASE WHEN q12 LIKE '%0%' THEN 1 ELSE 0 END) AS Search_engines,
    SUM(CASE WHEN q12 LIKE '%1%' THEN 1 ELSE 0 END) AS Video_sites,
    SUM(CASE WHEN q12 LIKE '%2%' THEN 1 ELSE 0 END) AS News_sites,
    SUM(CASE WHEN q12 LIKE '%3%' THEN 1 ELSE 0 END) AS Social_networking_sites,
    SUM(CASE WHEN q12 LIKE '%4%' THEN 1 ELSE 0 END) AS Browser_based_games,
    SUM(CASE WHEN q12 LIKE '%5%' THEN 1 ELSE 0 END) AS Shopping,
    SUM(CASE WHEN q12 LIKE '%6%' THEN 1 ELSE 0 END) AS online_banking,
    SUM(CASE WHEN q12 LIKE '%7%' THEN 1 ELSE 0 END) AS File_download ,
    SUM(CASE WHEN q12 LIKE '%8%' THEN 1 ELSE 0 END) AS Webmail,
    SUM(CASE WHEN q12 LIKE '%9%' THEN 1 ELSE 0 END) AS Forums,
    SUM(CASE WHEN q12 LIKE '%10%' THEN 1 ELSE 0 END) AS Adult_pages,
    SUM(CASE WHEN q12 LIKE '%11%' THEN 1 ELSE 0 END) AS Gambling ,
    SUM(CASE WHEN q12 LIKE '%12%' THEN 1 ELSE 0 END) AS Online_Word_Proccessing,
    SUM(CASE WHEN q12 IS NULL THEN 1 ELSE 0 END) AS null
FROM 
	survey s
WHERE s.q5::Integer=1 AND(s.q6::integer=1 OR s.q6::integer=2 OR s.q6::integer=3) AND (s.q8::integer>=8);






					-- Bookmarks(target MALE user) --

--How many BOOKMARKS do target MALE users HAVE? --
SELECT 
	user_id,
	event_code,
	s.q5 AS MALE_User,
	s.q6 AS AGE,
	s.q8 AS Skills,
	(SPLIT_PART(data1, ' ',1)::INTEGER) AS total_bookmarks,
	(SPLIT_PART(data2, ' ',1)::INTEGER) AS total_bookmark_folders,
	(SPLIT_PART(data3, ' ',3)::INTEGER) AS  maximum_depth_bookmark_folders
FROM 
	survey s
FULL JOIN events e USING(user_id)
WHERE event_code = 8 AND s.q5::Integer=0 AND(s.q6::integer=1 OR s.q6::integer=2 OR s.q6::integer=3) AND (s.q8::integer>=8)
GROUP BY 1,2,3,4,5,6,7,8;

-- Backcheck if Bookmark event 8 changes--
SELECT 
	user_id,
	event_code,
	data1
FROM events
WHERE event_code = 8;

--How many BOOKMARKS created do target MALE users HAVE that day? --
SELECT 
	user_id,
	event_code,
	s.q5 AS MALE_User,
	s.q6 AS AGE,
	s.q8 AS Skills,
	COUNT(data1) AS total_times_created_bookmark
FROM 
	survey s
FULL JOIN events e USING(user_id)
WHERE event_code = 9 AND s.q5::Integer=0 AND(s.q6::integer=1 OR s.q6::integer=2 OR s.q6::integer=3) AND (s.q8::integer>=8)
GROUP BY 1,2,3,4,5;


--How many times a target MALE users Choose a bookmark that day? --
SELECT 
	user_id,
	Count(event_code) AS bookmark_chosen,
	s.q5 AS MALE_User,
	s.q6 AS AGE,
	s.q8 AS Skills
FROM 
	survey s
FULL JOIN events e USING(user_id)
WHERE event_code = 10 AND s.q5::Integer=0 AND(s.q6::integer=1 OR s.q6::integer=2 OR s.q6::integer=3) AND (s.q8::integer>=8)
GROUP BY 1,3,4,5;

--How many BOOKMARKS modified do target MALE users HAVE that day? --
SELECT 
	user_id,
	event_code,
	s.q5 AS MALE_User,
	s.q6 AS AGE,
	s.q8 AS Skills,
	COUNT(data1) AS bookmark_modified
FROM 
	survey s
FULL JOIN events e USING(user_id)
WHERE event_code = 11 AND s.q5::Integer=0 AND(s.q6::integer=1 OR s.q6::integer=2 OR s.q6::integer=3) AND (s.q8::integer>=8)
GROUP BY 1,2,3,4,5;

-- total male users:
SELECT 
	COUNT(DISTINCT user_id)
FROM 
	survey
WHERE q5::Integer=0;
-- 3719 male survey users

--What Web Activities do Target MALe users do?
SELECT 
	COUNT(user_id) AS Number_of_target_MALE_user,
	SUM(CASE WHEN q11 LIKE '%0%' THEN 1 ELSE 0 END) AS Work_coding_related,
    SUM(CASE WHEN q11 LIKE '%1%' THEN 1 ELSE 0 END) AS Work_noncoding_related,
    SUM(CASE WHEN q11 LIKE '%2%' THEN 1 ELSE 0 END) AS School,
    SUM(CASE WHEN q11 LIKE '%3%' THEN 1 ELSE 0 END) AS Personal_life_assistance,
    SUM(CASE WHEN q11 LIKE '%4%' THEN 1 ELSE 0 END) AS Communication,
    SUM(CASE WHEN q11 LIKE '%5%' THEN 1 ELSE 0 END) AS Socializing,
    SUM(CASE WHEN q11 LIKE '%6%' THEN 1 ELSE 0 END) AS Entertainment,
    SUM(CASE WHEN q11 IS NULL THEN 1 ELSE 0 END) AS null
FROM 
	survey s
WHERE s.q5::Integer=0 AND(s.q6::integer=1 OR s.q6::integer=2 OR s.q6::integer=3) AND (s.q8::integer>=8);

-- What Websites do Target MALE users surf?
SELECT 
	COUNT(user_id) AS Number_of_target_MALE_user,
 	SUM(CASE WHEN q12 LIKE '%0%' THEN 1 ELSE 0 END) AS Search_engines,
    SUM(CASE WHEN q12 LIKE '%1%' THEN 1 ELSE 0 END) AS Video_sites,
    SUM(CASE WHEN q12 LIKE '%2%' THEN 1 ELSE 0 END) AS News_sites,
    SUM(CASE WHEN q12 LIKE '%3%' THEN 1 ELSE 0 END) AS Social_networking_sites,
    SUM(CASE WHEN q12 LIKE '%4%' THEN 1 ELSE 0 END) AS Browser_based_games,
    SUM(CASE WHEN q12 LIKE '%5%' THEN 1 ELSE 0 END) AS Shopping,
    SUM(CASE WHEN q12 LIKE '%6%' THEN 1 ELSE 0 END) AS online_banking,
    SUM(CASE WHEN q12 LIKE '%7%' THEN 1 ELSE 0 END) AS File_download ,
    SUM(CASE WHEN q12 LIKE '%8%' THEN 1 ELSE 0 END) AS Webmail,
    SUM(CASE WHEN q12 LIKE '%9%' THEN 1 ELSE 0 END) AS Forums,
    SUM(CASE WHEN q12 LIKE '%10%' THEN 1 ELSE 0 END) AS Adult_pages,
    SUM(CASE WHEN q12 LIKE '%11%' THEN 1 ELSE 0 END) AS Gambling ,
    SUM(CASE WHEN q12 LIKE '%12%' THEN 1 ELSE 0 END) AS Online_Word_Proccessing,
    SUM(CASE WHEN q12 IS NULL THEN 1 ELSE 0 END) AS null
FROM 
	survey s
WHERE s.q5::Integer=0 AND(s.q6::integer=1 OR s.q6::integer=2 OR s.q6::integer=3) AND (s.q8::integer>=8);