# Part 1 - Volume, Keyword and Contextual Analysis

## Objective

The objective of our volume, keyword, and contextual analysis is to determine how, and to what extent, different news outlets have reported on specific topics, as well as to identify the words that commonly appear in connection with these topics. This analysis serves as a foundation for understanding the contextual usage of keywords throughout our study. At this stage, the analysis does not yet allow for a precise interpretation of the semantic relationships between keywords and their context. However, it does reveal the underlying linguistic narratives through which specific topics are presented.

## Methodology

Article Volume and Trend by Source: In a first step, the reporting activity of individual news outlets is visualized over time using a line chart broken down by month. This allows for the analysis of both the volume and trend of coverage across the given time period.  
artikel_pro_monat = df.resample('M').size()  
artikel_pro_monat.plot()  
gesamt_article_count = df['full_article'].dropna().shape[0]  
Our objective here is to identify which events attracted the most attention of the individual newspaper outlets and thus triggered the publication of articles.

Topic Modelling - Individual and Collective Analysis: Next, we developed a topic model for each individual news outlet at the article level using the German-language model de_core_news_sm from spaCy. For each outlet, 20 distinct topics were generated. To ensure meaningful results, stopwords were removed prior to modeling, which was then conducted using lda_model. Visualizations were created by applying colormaps to the resulting graphs.  
In a subsequent step, we attempted to apply topic modeling at the paragraph level. For this, we installed sentence-transformers and scikit-learn, and used the BERT model 'all-MiniLM-L6-v2' to heuristically segment the texts in the CSV file into paragraphs. However, this process was ultimately unsuccessful, as no results were produced even after several hours of processing time.  
To complement the article-level topic modeling, we conducted an additional modeling step using a combined dataset containing all news outlets. This was done by merging individual CSV files stored in Google Drive. Using Google Colab, we first mounted the drive (*from google.colab import drive*, *drive.mount('/content/drive')*). Each file was read and then merged into a single DataFrame. Finally, the unified dataset was saved as a new CSV file: This consolidated file served as the basis for overall topic modeling across all sources.  To then analyze topic representation across different news outlets, we first computed topic distributions for the document using *lda_model.get_document_topics()*. After converting them into a DataFrame and adding the corresponding source information, we calculated the average topic distribution per outlet:  *avg_topic_distribution = topic_df.groupby("source")[topic_cols].mean()*. To identify the top-performing outlets per topic, we extracted the top 2 sources for each topic and stored them in a summary table. We manually reordered the sources for clarity according to political orientation from left to right. Finally, we created a heatmap using Seaborn to show topic intensity across outlets.     
Our objective here was to understand which topics co-occur within the same news articles, in order to identify whether certain news outlets associate specific topics with the different parties involved in the Gaza conflict more consistently—or inconsistently—than their counterparts.

Keyword Analysis - Genocide, Palestine and War Crimes: In this step, we examined how frequently the entities Genozid (Genocide), Kriegsverbrechen (War Crimes), and Palästina (Palestine) appeared in the reporting of each news outlet over time. For each article, we counted occurrences using regular expressions:  *df['genozid_count'] = df['full_article'].str.count(r'(?i)genozid')*, and similarly for the other terms. These counts were then resampled to calculate monthly totals:*df['genozid_count'].resample('M').sum()*. To normalize the data, we divided each monthly entity count by the number of articles published in that month, yielding relative mention rates per article: *genozid_relativ = genozid_monatlich / artikel_pro_monat*. Finally, we visualized the average mentions per article and per month in a line plot using Matplotlib, allowing us to compare how often each entity was referenced over time by the outlet.    
These terms were selected because their usage can serve as an early indicator of how news outlets frame the Gaza conflict—whether by implying accusations of unlawful conduct by Israel (Genocide, war crime), or by suggesting a recognition of Palestinian legitimacy (Palästina).

Contextual Analysis - Entity Context Window Analysis: In the final step, we analyzed which nouns and proper nouns appear immediately before and after the term Genozid (Genocide) and the conflict parties in the Gaza conflict, within articles from each news outlet. The choice of these entities is explained in Part 2 of the analysis. Using spaCy (*spacy.load('de_core_news_sm')*), we first extracted all nouns from the articles. We then searched for the target word (*genozid*) and collected the surrounding nouns within a context window of five words:  *kontext_fenster = 5*, *kontext = all_tokens[start:i] + all_tokens[i+1:end]*. Frequencies of these contextual nouns were counted using Python’s *Counter*, and the top 10 most common ones were visualized in a word cloud.       
We used the term Genocide to analyze the surrounding context in which it is discussed, whether it appears alongside more critical or accusatory nouns, or in reference to historical events. Similarly, we examined the main entities involved in the Gaza conflict to assess whether they are reported in proximity to more violent, war-related, or emotionally charged language. This helps reveal how different parties are linguistically framed across news coverage.

## Limitations 

Several limitations affected the implementation and scope of this analysis. First, paragraph-level topic modelling could not be completed due to extremely long processing times, which made it infeasible to run the code successfully. Since individual paragraphs often address specific subtopics, this limitation may have introduced inaccuracies in our article-level modelling approach. Second, the newspaper BILD contributed significantly fewer articles within the analyzed time frame. This data imbalance may have led to less reliable results for this outlet, particularly in the topic modelling and contextual analysis. Additionally, certain entities such as UNRWA and ICC were mentioned too infrequently to allow for meaningful interpretation of their linguistic context. Finally, due to unresolved technical errors, most parts of the analysis, except for the overall topic modelling, had to be conducted in separate Colab notebooks and using separate files. This fragmentation limited the possibility of a fully integrated codebase for the project.  

➡️ The code for this analysis can be found here: [Die Welt](https://colab.research.google.com/drive/1MOf7UY0OCUE8bo4rF4Lp9Dp4Y1GcIqrg?usp=sharing), [Süddeutsche Zeitung](https://colab.research.google.com/drive/1SPNVgH4ZnG-c_04862AXoMqtfc83DV36?usp=sharing), [Frankfurter Rundschau](https://colab.research.google.com/drive/1o_ecsPKQshnxAJPMD6UVNyaRfyMXewYj?usp=sharing), [taz - die Tageszeitung](https://colab.research.google.com/drive/1Hg9dCq400P4VuCtbPe6r84y4t9I3_p-1?usp=sharing), [topic modelling all sources](https://colab.research.google.com/drive/1-XCnxfuFwjOTTcox3lyiCu2-k-FMxdxi?usp=sharing)

## Discussion of Results

### 1. Article Volume and Trend by Source

To examine the extent of media coverage concerning the overlying issue, it is useful to carry out an analysis of the article volume and trends by the individual news sources. 

Bild:Number of Articles per Month per Source

The analysis above demonstrates that no individual media outlet represents a significant outlier in its trend of coverage concerning the conflict. Throughout the entire time period observed, the tabloid BILD has reported the least, while the Süddeutsche Zeitung has consistently covered the issue most extensively, closely followed by the taz. There was a small but visible peak of coverage following the request for an ICC arrest warrant for PM Netanyahu in May 2024 within all sources except the Süddeutsche Zeitung, and again a negligible increase in coverage following the anniversary of the start of the conflict. Concerning the latter, only the Süddeutsche Zeitung and Frankfurter Rundschau seemed to increase their coverage until January 2025, when the article volume reached another peak, but has not picked up significantly during South Africa’s application to the ICJ, arguing for an existence of a Genocide carried out by the Israel. Interestingly, all five news sources already exhibit a very steep decline in coverage a month after the October 7th attacks, and fluctuated around an approximately equally low level throughout the following year. All in all, while differing in general volume of coverage, the different news sources follow very similar trends, suggesting they were all objectively influenced by general events and do not display individual shifts in incident-related attitudes or priorities. 

### 2. Topic Modelling - Individual and Collective Analysis

#### A. Article - Level Topic Modelling by Source

In the first step, 20 topics were generated for each news outlet using article-level topic modelling. The results are presented in the following section, starting with the most conservative newspaper and proceeding toward the more leftist and system-critical ones—concluding with BILD, which is treated separately as a tabloid outlet.

Die Welt

Bild: Topic-Modelling Welt

A striking feature of Die Welt’s reporting is the prominence of the topic hostages, which frequently appears in connection with Iran. This suggests that the newspaper portrays the Iranian state as involved in the hostage situation. More broadly, Iran is mentioned across several topics, not only alongside hostages, but also in association with Hezbollah and Lebanon, which represent the second conflict front in the ongoing Israel conflict. Furthermore, Die Welt places strong emphasis on the international community, as entities such as Trump, USA, Germany, Russia, China, and Biden regularly appear within the same topics as direct conflict-related terms like hostages. Lastly, it is notable that terms such as Gaza Strip or Gaza are rarely featured as central topic elements. Furtermore, words like Palestinian are primarily used in the context of hostages or international actors, rather than in association with victimhood or state-related terminology. Concludingly, the media coverage seems to concentrate on the hostage situation as well as the international aspects of the conflict.

Süddeutsche Zeitung

Bild: Topic-Modelling Süddeutsche Zeitung

In the case of the Süddeutsche Zeitung, a notable difference from Die Welt is the significantly more frequent use of the terms Palestinians and Palestine, which appear far more prominently across the identified topics. However, despite this increased visibility, these terms are still predominantly embedded in contexts that focus on hostages or on international actors, including Trump, Erdogan, Biden, Iran, and Germany. Much like in Die Welt, there is no consistent linguistic framing of Palestine in terms of statehood, victimhood, or human suffering. Similarly, the term Gaza Strip does not appear as a central element in any of the topics derived from the analysis. More generally, the United States emerges as a recurring actor across the topic landscape, largely represented by the figures of Biden and Trump, suggesting that the newspaper frames the U.S. as a central party in the Gaza conflict. Similar to Die Welt, Germany is mentioned across several topics, often in connection with terms such as Jewish or antisemitism, indicating that the Süddeutsche Zeitung linguistically embeds the German state's historical and political responsibility in discussions related to the conflict.

Frankfurter Rundschau

Bild: Topic-Modelling Frankfurter Rundschau

The Frankfurter Rundschau presents a noticeably different thematic and linguistic profile compared to the previously discussed outlets. While topics such as hostages in connection with Palestinians, or the international community in relation to conflict-related terminology, continue to appear – albeit less dominantly – the focus in this newspaper shifts more clearly toward the Gaza Strip. The term Gaza surfaces more frequently and is notably accompanied by words such as Palestinians, side, land, and state. This suggests that the Frankfurter Rundschau offers greater linguistic space to the Palestinian perspective, and does so in a way that is not solely tied to hostage-related narratives. Germany and antisemitism continue to appear together as thematically linked elements, as was also the case in the Süddeutsche Zeitung and Die Welt. However, what distinguishes the Frankfurter Rundschau is a recurring pattern where terms such as life, Palestinian, Gaza Strip, and child co-occur in several topics. This points toward a more human-centered framing, suggesting that the humanitarian conditions in Gaza receive more explicit attention. Overall, the Frankfurter Rundschau seems to linguistically broaden the framing of the conflict by incorporating not only geopolitical and security-oriented narratives, but also those highlighting civilian life and suffering on the Palestinian side.

taz- die Tageszeitung

Bild: Topic-Modelling taz

The trend observed in the Frankfurter Rundschau becomes even more pronounced in the taz – die tageszeitung. Here, the Gaza Strip emerges as an even more central theme—not only in association with the hostage narrative, but especially in connection with words such as life, land, family, child, and Palestinians. Notably, the West Bank is also mentioned for the first time with higher frequency in connection to Gaza Strip, suggesting that the newspaper frames the conflict in a way that emphasizes the entirety of the Palestinian territories rather than focusing solely on Gaza. Another distinctive feature of the taz is the repeated co-occurrence of the term Jewish with references to the army. This implies that the Israeli side is also portrayed through the lens of military action, possibly signaling a more critical stance toward state violence and power. Furthermore, the word Germany appears in conjunction with life, woman, child, Gaza Strip, and Palestinians, indicating that the role of the German state is linguistically positioned in relation to the fate and suffering of civilians in Gaza. This thematic and linguistic framing aligns with the taz's traditionally system-critical editorial stance and suggests a heightened sensitivity to humanitarian connected to geopolitical dimensions.

BILD

Bild: Topic-Modelling Bild

A notable pattern in BILD’s coverage is the frequent inclusion of the topic of terrorism, a term with strong linguistic and emotional connotations, aligning with the paper’s characteristic tone. This framing often appears in connection with the term hostages, which may suggest an implicit condemnation of Hamas’ actions. Additionally, it is striking that in most topics related to the Gaza conflict, either Germany or Berlin appears prominently, indicating that BILD linguistically frames the German state as highly involved in the conflict. In the case of BILD, we limited our analysis to only 10 topics. This decision was due to the significantly lower number of published articles compared to the other outlets, which meant that generating a full set of 20 meaningful and distinct topics was not feasible. 

#### B. Collective Topic Modelling - Weights Analysis per Topic

As a second step, we conducted a cross-outlet topic analysis based on the merged dataset. By calculating the average topic distributions per source, we aimed to identify thematic differences in coverage across media outlets.

Bild: Heatmap - Collective Topic-Modelling

In order to draw meaningful conclusions from the analysis, particular attention should be paid to topics that display significant differences in representation across media outlets.

Topic 4  

Topic 4 is characterized by the frequent co-occurrence of terms such as Palestinian, Germany, life, state, and German government. In the heatmap, it is particularly notable that the left-leaning outlets, taz and Frankfurter Rundschau, score significantly higher on this topic compared to their counterparts. This observation supports the findings from the previous analysis, which highlighted that these two news outlets provided more extensive coverage of the topic of Palestine and Palestinians in a broader context, including issues related to daily life, state terminology, and the influence of the German government on Palestinian affairs.

Topic 8  

Topic 8 is characterized by the frequent co-occurrence of terms such as child, hostage, man, woman, house, family and life. For this topic, only Bild achieves a higher score, while other news outlets do not strongly represent it. This can be attributed to the fact that Bild employs simpler, more visual language, using straightforward terms to attract readers.

Topic 10 

Topic 10 is characterized by the frequent co-occurrence of terms such as gaza-strip, palestinian, government, army, state, land, westjordanland. Once again, a clear trend emerges: the more left-leaning and system-critical the news outlet, the higher the score. This aligns with the findings from the previous analysis, where newspapers with a more critical stance towards the establishment tended to report more extensively on the Gaza Strip including a stronger focus on state-related terminology, as well as discussions around Palestine and the West Bank. 

Topic 11 

Topic 11 is characterized by the frequent co-occurrence of terms such as Iran, USA, state, Arab, Qatar, president, government. Here, in contrast to the previous topics, a reversed pattern emerges: the more conservative the news source, the stronger the emphasis on this particular topic, with Die Welt being the most prominent. These results align with previous observations, where conservative news sources tend to focus more on the international geopolitical situation and highlight global actors. 

Topic 19

Topic 19 is characterized by the frequent co-occurrence of terms such as Iran, regime, land, Tehran, Hizbollah, Lebanon. As in the previous case, conservative news outlets score higher here as well. This observation aligns with the findings from the earlier analysis, where it was shown that these outlets place greater emphasis on Iran and the second front of the Israel conflict, particularly in relation to Gaza.

### 3. Keyword Analysis - Genocide, Palestine and War Crimes

Examining the amounts of times where Genocide (Genozid), Palestine (Palästina) and War Crimes (Kriegsverbrechen) were mentioned throughout the examined time period is highly interesting, as it provides an idea of the level of legitimacy each source prescribes to the Palestinian cause, regardless of the nature of the reporting. The results of the individual news outlets are presented below. 

Die Welt  

Bild: Welt Keyword Analysis

Süddeutsche Zeitung

Bild: SZ Keyword Analysis

Frankfurter Rundschau

Bild: Frankfurter Rundschau Keyword Analysis

taz - die Tageszeitung

Bild: taz Keyword Analysis

BILD

Bild: BILD Keyword Analysis

Analysis  

Regarding the word Genocide, the BILD exhibits the highest peak of mentions  surrounding the time of South Africa’s Genocide ruling application in January 2024. For a tabloïd newspaper this is surprising. The taz and Die Welt also show a peak of coverage including the word in January 2024, suggesting that the issue was debated on both sides of the political spectrum. The Süddeutsche Zeitung and the Frankfurter Rundschau demonstrate fewer and less intensive peaks of the word in general, and barely increase the coverage around the time of the ICJ request either expressing less interest in this legal controversy, or prefer to go around clear statements concerning Genocide alltogether. The BILD quickly stops reporting on issues surrounding Genocide completely, with the exception of september 2024. This coincides with the UN GA resolution which was passed at the end of this month, concerning the ICJ’s advisory opinion on Israel's actions in the Occupied Palestinian Territory. It takes the other newspapers a few weeks to report on the issue using the word Genocide, with the Süddeutsche Zeitung using the word the most, followed by Die Welt and Frankfurter Runschau. All in all, Die Welt exhibits more frequent peaks of mentions throughout the year than the taz, which only had one period where the word was mentioned strikingly often (January 2025). The Frankfurter Rundschau and Süddeutsche Zeitung exhibit an overall smaller weaker position on the issue, with the word generally mentioned at fewer times with smaller peaks. While there are some times where all newspapers mention the word more often, there is less coherence among them throughout the year, with some newspapers exhibiting many peaks at various times, while others follow a very different (but not counter-indicative) trend.    
With the exception of BILD, most newspapers appear more willing to use the term Palestine than Genocide throughout the year. Several outlets show peaks in Palestine mentions that align with Genocide-related trends only in the cases of Die Welt and Süddeutsche Zeitung. In contrast, taz refers to Palestine far more frequently on average, with mentions surpassing 1.3 per article at certain points, yet these references show only a weak correlation with Genocide, indicating that the newspaper may draw this connection less often. While some outlets, such as taz and Frankfurter Rundschau, mention Palestine most often around May 2024 during the request for Netanyahu’s arrest warrant, others, like Die Welt and Süddeutsche Zeitung, do so in September 2024, coinciding with the UN GA resolution genocide ruling.    
Lastly, concerning the words War Crimes all newspapers show very few mentions, with the BILD exclusively mentioning these words in spring 2024. The only noticeable peak that is visible across all other newspapers appears at the end of 2024, likely as a consequence of the UN GA resolution on the genocide ruling in September 2024.

### 4. Contextual Analysis - Entity Context Window Analysis 

In a final step, we conducted an analysis examining which nouns most frequently appear before and after Genocide as well as the respective conflict parties, as defined in the methodology, in the articles of each news outlet. Results are not reported for the entities UNRWA and ICC, as these topics were not mentioned frequently enough to produce reliable data. The same applies to the Israeli army, which was therefore included under the broader entity Israel in the analysis. In the section on the respective conflict parties, Bild was not included in the analysis, as the limited number of articles did not provide sufficient data to yield meaningful results.

#### A. Genocide

Die Welt

Bild: Wordcloud Die Welt

Süddeutsche Zeitung

Bild: Wordcloud Süddeutsche Zeitung

Frankfurter Rundschau

Bild: Wordcloud Frankfurter Rundschau

taz - die Tageszeitung

Bild: Wordcloud taz

BILD

Bild: Wordcloud BILD

Analysis

For Die Welt, the nouns most commonly appearing in the context of Genocide are land, October, and Palestinians. To a lesser extent, terms such as apartheid, Jews, and Israelis are also mentioned. This suggests that the term Genocide is primarily discussed within temporal and geographical frames (such as the month of October and the notion of land), and in relation to its impact on the Palestinian side. In the case of Süddeutsche Zeitung, the topic of apartheid appears more frequently, likely in relation to South Africa’s case at the ICJ mentioned earlier. Particularly notable is the recurrence of words such as terms, word, and procedure, which suggests that the SZ places an emphasis on the legal and definitional process through which the label of Genocide is determined and established. In the reporting of the Frankfurter Rundschau, the dominant terms surrounding the concept of Genocide are campus, police, protests, and state security, which suggests that the focus was placed on student protests against Israel and accusations of Genocide in the Gaza Strip. In the case of the taz, the dominant terms surrounding the notion of Genocide were cleansing, settlers, and apartheid. This indicates that the newspaper placed a strong emphasis on describing and thematizing the specific acts and defining characteristics of Genocide in its reporting. Ultimately, the coverage by Bild primarily focuses on terms like applause, highlight, festival, and Berlinale. This suggests that the outlet's limited reporting on the topic of Genocide was skewed by the incident at the Berlinale in February 2025, when a director attributed partial responsibility for the alleged Genocide in Gaza to the German state.

#### B. Parties to the Conflict

Die Welt

For Die Welt, Netanyahu is primarily associated with terms like civil war, Lebanon, world, Hezbollah response, law, and UN. This indicates a focus on the broader regional context and the political and legal dimensions of the conflict. The Hamas entity is frequently linked with Hezbollah, Iran, staff, Israel, conflict party, hostages, and Houthi rebels, suggesting a portrayal of Hamas as a key player in the regional and global dynamics of the conflict. The term Israel is predominantly associated with states, Jews, regime, millions, Hezbollah, and Egypt, pointing to a broader geopolitical narrative, which involves both religious and regional power struggles. For Iran, the most common associations are with Lebanon, Hezbollah, Tehran, militias, Syria, USA, and region, highlighting Iran's significant role in supporting proxy groups and its involvement in the Middle East.

Süddeutsche Zeitung

The Süddeutsche Zeitung presents a slightly different perspective, with Netanyahu being associated with end, east, October, Biden, officials, and Istanbul. This suggests a focus on political timelines and international diplomacy. The entity Hamas is most often linked to Israel, word, Palestine, group, Solomon, and men, emphasizing the ongoing ideological and territorial conflict. Comparable to Welt, Iran is linked to Hezbollah, Lebanon, Tehran, regime, region, and USA, showing similar patterns in terms of Iran’s support for non-state actors in the region. Israel is frequently connected with Germany, state, Iran, October, Jews, USA, Ukraine, and land, reflecting the intersection of European geopolitics and the conflict, as well as the broader international involvement.

Frankfurter Rundschau

The Frankfurter Rundschau focuses more on the political dimensions and individual responsibilities within the conflict. For Netanyahu, key terms include end, future, Israel, prime minister, failure, detention, responsibility, and victory, suggesting a critical stance on the Israeli leadership. Hamas is primarily associated with Houthi, militia, solidarity, Hezbollah, and container shipping, indicating a connection to other militant groups and regional logistical routes. As with other outlets, Iran is most frequently associated with Lebanon, Hezbollah, Israel, Syria, and USA, reflecting its central role in supporting proxy forces. Israel is linked to correspondence, perpetrators, military, police, Germany, and book, which suggests a more critical stance on the state's actions, with some focus on the international diplomatic and legal aspects of the conflict.

taz - die Tageszeitung

In the taz - die Tageszeitung, Netanyahu is often associated with October, timepoint, Israel, prime minister, and Gaza Strip, emphasizing the temporal and territorial elements of the conflict. Hamas is connected to Erdogan, Berlin, and student discussions, suggesting an international focus on the political and ideological solidarity with Palesine. For Iran, the key terms are Hezbollah, Lebanon, region, regime, USA, militias, Israel, and Syria, again reflecting a critical stance on Iran's influence and regional dynamics. Finally, Israel is associated with Palestine, USA, October, Jews, Israel, Gaza Strip, and world.

Analysis

In summary, it can be concluded that the analysis does not yield different results for the actor Iran. However, the word analysis shows that the left-wing Frankfurter Rundschau and the system-critical taz apply less critical terms to Hamas compared to their pendants. Instead, they focus on topics such as solidarity, which are not present in the reporting of Welt and Süddeutsche Zeitung. These outlets show a more critical stance towards Hamas and primarily emphasize the regional and international dimensions of the conflict in relation to Netanyahu and Israel, whereas the analysis of taz and Frankfurter Rundschau showed more condemnatory nouns such as perpetrator or failure, or a direct connection to the Gaza Strip. Since Task 2 allowed for the conclusion that reporting on Gaza Strip here was often connected to humanitarian or state-related issues, voiced criticism towards the entities Israel and Netanjahu seems further possible.

### Interpretation

While Part 1 (article volume and trend by source) and Part 3 (keyword analysis) did not reveal any clear or consistent patterns regarding how news coverage varies depending on the political orientation of each outlet, and even demonstrated considerable overlap in terminology and focus across ideologically diverse media, Parts 2 and 4 of the analysis did produce more differentiated results.  
In Part 2, topic modeling was used to identify thematic structures within each outlet’s reporting. Here, a notable trend emerged: the further left-leaning or system-critical a publication was, the more frequently and prominently it addressed topics directly related to Palestine, the Gaza Strip, and humanitarian consequences of the conflict. For example, Frankfurter Rundschau and taz linked Gaza Strip to terms such as children, families, West Bank, and Palestinians, suggesting a focus on civilian suffering and statehood. These outlets also often associated Germany with these terms, indicating a critical reflection on Germany’s position and responsibilities within the conflict. In contrast, more conservative outlets like Die Welt and Süddeutsche Zeitung showed a stronger focus on hostage situations, Iran, and other international geopolitical actors. such as Hezbollah, and the United States, suggesting that these outlets contextualize the conflict more in terms of security, regional power dynamics, and global diplomacy. Notably, mentions of Gaza and Palestine appeared mainly in association with hostages, rather than with humanitarian or statehood discourses.  
Part 4, the contextual noun analysis, supported and deepened these findings. When examining the nouns surrounding terms like genocide, as well as named conflict actors such as Israel, Hamas, Iran, and Netanyahu, further distinctions became evident. Conservative outlets tended to frame Hamas in a more critical or militant context, often accompanied by words like hostages, conflict, or rebels. Meanwhile, Israel was typically discussed in institutional or geopolitical terms, frequently linked to state, Jews, or regime. By contrast, taz and Frankfurter Rundschau placed more emphasis on protest movements, solidarity actions, and terminology like apartheid, settlements, and cleansing, particularly in the context of genocide. This implies a more active engagement with moral discourse, as well as a higher degree of skepticism toward Israeli narratives. Even in the treatment of actors like Netanyahu or Iran, ideological positioning seemed to shape the discourse. While all outlets acknowledged Iran's role in the broader conflict, the extent and framing of Netanyahu varied: conservative outlets emphasized his central position in a conflict with an international dimension, critical-left outlets linked the Prime Minister more often to words like detention of responsibility.   

# Part 2 - Attribution Analysis: Detecting Blame and Criticism

## Objective

The objective of our attribution analysis is to identify how blame and criticism have been assigned - both explicitly and implicitly - to specific actors and entities in the reporting on the war in Gaza across the selected German media outlets. 

While the analysis does not seek to determine whether newspapers intentionally disseminate these criticisms and accusations, it does detect acts of quoting, amplifying or highlighting critical voices, which constitute an indirect form of attribution of responsibility. The aim is therefore to uncover and compare these discursive mechanisms of reproach across different outlets to better understand the differential roles German media outlets play in constructing and shaping narratives around accountability. 

## Methodology

Entity Identification: According to our personal judgment on their relevance to the conflict, we selected different key entities that might be subject to blame or criticism. These included (1) state entities (Israel, Iran), (2) armed groups and military actors (Hamas, Israel Defense Forces), (3) political leaders (Netanyahu, Gallant), and (4) international bodies (UNRWA, ICC). For each entity, we included multiple variations of their names or references as they might appear in German media and then organized them into standardized categories, enabling consistent analysis. 

Linguistic Pattern Recognition: To detect language patterns indicating blame or criticism, we used several complementary approaches. These included the creation of specific regular expression patterns, designed to capture German grammatical constructions and extensive lists of German verbs and keywords signaling blame or criticism. Moreover, we ensured to account for German grammatical variations, gender agreement, and tense forms to ensure comprehensive coverage. Finally, using the German language model from spaCy (de_core_news_md), we performed dependency parsing to analyze the grammatical structure of sentences, allowing us to identify when target entities where objects of blame verbs as well as to detect more complex blame constructions. 

Sentence Extraction and Comparative Analysis:  For each article, our system split the text into individual sentences and applied both pattern matching and dependency parsing to each sentence. It then detected sentences containing blame attribution to our target entities and categorized which entities were being blamed in each instance. For instance, in a sentence like: “Ein Minister warf der Hamas Versagen vor” (“A minister accused Hamas of failure”), the parser would identify Hamas as the target of vorwerfen (“accuse”) and thus log a blame instance for Hamas. These sentences were then aggregated by entity and source and lastly normalized to allow for comparisons across different news outlets.

## Limitations

While our analysis aims to uncover how blame and criticism are assigned - explicitly and implicitly - across different German media outlets, it is important to recognize the methodological limits of this approach. While our focus on attribution, rather than intention, allows us to map how narratives of accountability appear across different German sources, our model does not systematically capture criticism expressed directly by the newspaper’s 
editorial voice. As a result, forms of more overt or unmediated criticism may go undetected, while the presence of many attributed statements may reflect a cautious or distanced reporting style. Furthermore, the model occasionally misattributed blame due to its inability to interpret the broader context or rhetorical purpose of a quotation. Thus, a core limitation of our approach is that while we capture the presence and frequency of attributed criticism, we do not always distinguish whether these attributions function to amplify, distance from or delegitimize the critical statement. 

➡️ The code for this analysis can be found here: **[LINK]**

## Discussion of Results

### 1. General Findings

Across all media outlets we analysed, the balance of blame suggests that much of the press disscourse - regardless of political orientation - placed a strong focus on Israeli actions. In fact, Israel was the most frequently blamed actor, appearing in 57.1 % of all blame attributions (425 instances), (see **figure 1**). Whilst the data does not suggest Israel being depicted as the sole villain in media, it reflects that many commentaries dealt with Israeli responsibilities, failures, or wrongdoings in the war. One reason for that could be that as the main and only state actor involved in the conflict, responding with military force, international scrutinity is drawn upon it, and German media outlets echoed that by reporting criticism of Israeli conduct with regards to civilian casualties, breakdown of ceasefires, etc. 

Simultaneously, Hamas, as the opposing party in the war and initiator of the violence and other acts was clearly recognized as culpable with 25.1% of blame attributions (187 instances) across the corpus (see **figure 1**). Perhaps these instances were fewer because much more self-evident or were less frequently explicated in articles after the initial events. In fact, the data suggests that after the initial Hamas attack of Ocotber 7th 2023, the media conversation quickly shifted and broadened to hold Israel’s government accountable for its actions (e.g., security lapses, military escalations, humanitarian crises). This trend was consistent across outlets (see **figure 1**): in taz 55.5% of blame instances targeted Israel, and 29.8% targeted Hamas. Frankfurter Rundschau showed a similar split (60.3% Israel, 19.0% Hamas), as did Süddeutsche Zeitung (58.6% Israel, 23.3% Hamas). Even conservative outlets followed this pattern: Die Welt had 51.4% of its blame attributions directed at Israel versus 27.9% at Hamas.

![Figure 1: Blame Attribution Summary](https://github.com/robinmadani/gaza-research-project/blob/main/figure%201_attribution%20analysis.png)
#### Figure 1

### 2. Media Outlet Comparison

![Figure 2: FR & taz Comparison](https://github.com/robinmadani/gaza-research-project/blob/main/figure%201_attribution%20analysis.png)
#### Figure 2

### 2.1 Frankfurter Rundschau & taz

When comparing outlets, we see nuances that correlate with their known editorial leanings. More progressive and left-leaning papers like the Frankfurter Rundschau and taz indeed devoted a larger share of blame to Israel relative to Hamas than did the right-leaning ones (see **figure 3**). FR, for instance, attributed over three times as many blame statements to Israel as to Hamas (60% vs. 19%), which would be consistent with a human-rights-oriented perspective that scrutinizes Israli actions heavily. Likewise, taz emphasized Israeli culpability, though given it also had significant amounts of Hamas blame, which points to a balanced critical coverage that does not exonerate Hamas role in the war. These patterns align with the expectations that progressive outlets would be more critical of the Israeli government’s conduct in the war while also condemning Hamas to a lesser extent. We safely assume this, because Israel’s use of violence numerically exceeds that of Hamas. This is exemplified with the Palestinian death toll exceeding the Israeli death toll by a factor of approximately 55 (Al-Jazeera, April 2025).

### 2.2 Die Welt & BILD

On the other side, we expected Die Welt and BILD, traditionally supportive of Israel, to emphasize Hamas and even Iran (in systemical rivalry with Israel), which would be in line with a conservative narrative that highlights threats Israel faces. Quite surprisingly, Die Welt still had slightly over half of its blame instances (51.4%) targeted at Israel itself (see **figure 2**), indicating that it indeed reported on Israeli responsibility, although the nature of this criticism cannot be determined - perhaps by quoting international voices or covering Israeli internal debates.

We want to draw particular attention to the case of BILD. While it is known for its unwavering pro-Israel stance—mandated by its publisher Axel Springer, which requires editors to uphold Israel’s right to exist (Byline Times, November 2023)—BILD nonetheless recorded 91% of its blame attributions toward Israel. However, this figure is based on only eight articles and should be interpreted with caution. Given the low incidence of blame statements detected (only some 8 articles, and most references to Israel), this suggests that BILD’s own editorial voice avoided to attribute blame to Israel or Hamas in explicit terms. The instance that we did detect likely from BILD quoting others as for instance reporting on an anti-Israel slogan at a protest or reproducing a Hamas statement blaming Israel. We found evidence in the data accusing Netanjahu and “his Nazi-army” of killing hostages, which BILD frames as a “Täter-Opfer-Umkehr” (perpertrator-victim reversal) by terrorists: 

“It also states in English: “The war criminal Netanyahu and his Nazi army killed them with rockets from Zionist warplanes.” A perpetrator-victim reversal, in which the terrorists attempt to blame Israel for the death of the hostages.”

This shows that BILD content may contain statements blazing Israel not as endorsement but to condemn those very statements. On the contrary, the outlets’ single instance of blame directed at Hamas: “Die Hamas nutzt das unschuldige Lächeln, die Einheit der Familie als Waffe”, (“Hamas uses the innocent smile, the unity of the family, as a weapon.”), likely reflects BILD’s own condemnatory tone towards Hamas. We must understand the context of the data: the high proportion of blame towards Israel is only a trace of quoting adversarial narratives, not of BILD actually criticiszing Isarel in its own editorial voice. 

Furthermore, the absence of more granular blame targets - such as Iran, the IDF, UNRWA, or Netanjahu - suggests a limited engagement with the broader complexity of the conflict. These omissions point to a preference for binary narratives (e.g., victim vs. perpetrator, good vs. evil) over more nuanced accounts. BILD tends to favor emotionally charged, populist framing, where engaging with institutional responsibilities or geopolitical context may be seen as detracting from its core messaging. The lack of criticism directed at figures like Netanjahu or the IDF likely reflects editorial constraints tied to BILD’s historically pro-Israel stance. With only 181 articles in total, and just 8 containing any blame attribution, BILD’s reporting appears notably shallow in scope, indicating a surface-level engagement likely shaped by editorial priorities.

### 2.3 Süddeutsche Zeitung & Frankfurter Rundschau

Left-of-center outlets were consistently more critical of Israeli actions, as reflected in their higher share of blame attributions toward Israel. This aligns with their traditional editorial stances, which tend to advocate for de-escalation and emphasize human rights concerns. However, it is important to contextualize this within the broader media tone in Germany, which was marked by widespread solidarity with Israel across the political spectrum. Some commentators noted that newspapers “from left to right” appeared to follow a line of “unconditional support for Israel,” reflecting Germany’s historical and political responsibility stemming from the Holocaust (Byline Times, April 2023).

In that sense, the prevalence of Israeli-blame narratives may seem counterintuitive. Yet, this can be understood as a distinction between editorial stance and reportage: while most outlets upheld Israel’s right to defend itself following the October 7th attacks, their reporting did not shy away from documenting perceived failures or controversial actions by the Israeli government. For example, Süddeutsche Zeitung published an interview in which an Israeli citizen stated, “I don’t give Hamas the blame, I give my government the blame,” directly criticizing Israel’s security failings. Although the frequency of blame attributions does not necessarily reflect strong moral condemnation, it shows that even outlets broadly supportive of Israel were willing to include perspectives critical of its conduct—particularly regarding accountability for the war’s progression and its consequences.

In sum, the media upheld Israel’s legitimacy and right to defend itself at the editorial level, yet reported extensively on the government’s accountability for the outcomes of the war. This produces a nuanced narrative in which Hamas is morally condemned for its initial aggression, while Israel is subjected to practical scrutiny for its conduct during and after the conflict. Whether the level of criticism directed at each actor corresponds to the actual moral severity of their political and military actions, however, remains an open question and cannot be determined from this data alone.

The data also suggests that Hamas’s culpability was rather treated as a given and hence less frequently subject of editorial criticism. A reason for this could be that Hamas’ attack on October 7th was universally condemned across German media outlets and its clearly attributable role of initiator of the military confrontation, made it little necessary to assign blame for what was evident, beyond the initial reports on the attack. Instead, coverage quickly moved to Israel’s government. We argue that many blame attributions related to Hamas may have been phrased not as “blaming Hamas”, but as simple descriptions of Hamas’ acts (e.g., “Hamas attacks Israel”), that can be understood as factual statements, not as blame attributions in the linguistic sense captured by our method.

While Israel’s role in the war evolved—from victim of terror attacks to active military actor—Hamas’s role was more clear and became discursively less prominent after the initial aggression. In contrast, discussions about responsibility for civilian suffering or political failures often involve explicit assignments of blame to Israeli authorities, hence getting counted more often. This could explain why even outlets strongly aligned with Israel still had numerous instances of blame directed at Israel – they were often discussing who is responsible for a ceasefire collapsing, or for a security oversight, etc., questions that by nature involve Israel’s role.

### 2.4 Temporal Evolution of Blame

![Figure 3: Timeline Overview](https://github.com/robinmadani/gaza-research-project/blob/main/figure%203_attribution%20analysis.png)  
#### Figure 3
![Figure 4](https://github.com/robinmadani/gaza-research-project/blob/main/figure%204_attribution%20analysis.png)
#### Figure 4
![Figure 5](https://github.com/robinmadani/gaza-research-project/blob/main/figure%204_attribution%20analysis.png)
#### Figure 5
![Figure 6](INSERT_IMAGE_PATH_HERE)
#### Figure 6
![Figure 7](INSERT_IMAGE_PATH_HERE)
#### Figure 7

According to our timeline analysis (see **figure 3 to 7**) temporal trends further enlighten us on narrative shifts as the war evolves, however only allow us to make inferences about likely patterns, as we lack detailed timeline data per source. The overall trajectory hints at an initial convergence on blaming Hamas (for being the aggressor that started the war), followed by a sustained narrative of holding Israel accoutable for how the war was conducted. This temporal dynamic illustrates how responsibility is constructed in stages: from the initial cause vs. the ongoing course of events - and the German media tracked boht phases, with a notable emphasis on the latter in the long run.


# Quellen

Al-Haq  |  Defending Human rights in Palestine since 1979. « UN General Assembly Member States Must Stand Against Israel’s Occupation, Apartheid and Genocide ». Consulté le 8 mai 2025. https://www.alhaq.org/advocacy/24398.html.

Kottasová, Ivana. « Exclusive Interview: ICC Prosecutor Seeks Arrest Warrants against Sinwar and Netanyahu for War Crimes over October 7 Attack and Gaza ». CNN, 20 mai 2024. https://www.cnn.com/2024/05/20/middleeast/icc-israel-hamas-arrest-warrant-war-crimes-intl. 

« Order of 26 January 2024 | INTERNATIONAL COURT OF JUSTICE ». Consulté le 8 mai 2025. https://www.icj-cij.org/node/203447.

« Staatsschutz ermittelt nach Völkermord-Vorwurf auf der Berlinale », 18 février 2025. https://www.rbb24.de/kultur/berlinale/beitraege/2025/staatsschutz-ermittelt-voelkermord-vorwurf-berlinale-regisseur-jun-li.html.
