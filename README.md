## Inspiration
In recent years, there has been a sharp rise in consumer exposure to misleading and exaggerated facts online. This phenomenon has been particularly poignant in social media platforms such as YouTube which have been criticized for [distorting the truth](https://www.theguardian.com/technology/2018/feb/02/how-youtubes-algorithm-distorts-truth) and propagating misinformation. Many social media platforms have attempted to solve this problem by adjusting their recommendation algorithms, but no matter their efforts, misleading videos seem to pass them by.

## What it does
Polygraph is designed to counter this: it timestamps and captions dubious claims in real time, overturning them with links to reliable statistics or articles to refute them. As a chrome extension, it removes the need for users to manually input video links or download the video themselves; it detects the presence of a YouTube webpage, and will scan for false claims at the click of a button.

## How it works
Using documentation provided by google, the chrome extension is built with CSS, HTML, and node. Upon detecting a YouTube video from its webpage link, API requests are sent to YouTube to access the captioning of that video. 
These timestamped captions are then sent to Polygraph's API built with Flask.

Polygraph's API first identifies which identifies the sentences that assert a factual claim using [ClaimBuster](http://www.vldb.org/pvldb/vol10/p1945-li.pdf). A selection of sentences that are most likely to contain factual claims are then passed to the next step.

With these sentences, we then draw out the key phrases using Azure's Key Phrase API. This step is crucial because it allows Polygraph to narrow down a large claim to just a few words, and find related articles.

We then verify our results using Google's factcheck tools combined with [textual entailment](https://arxiv.org/abs/1907.11692) provided by AllenNLP. 
Claims that don't pass scrutiny are then passed back, along with the article that debunks these false claims. These are passed to the chrome extension, which then lists them, along with timestamps and source links.

[Here's a diagram of the project layout](https://lucid.app/lucidchart/invitations/accept/d480e272-09fb-4cb1-9082-bf4cb598ef23)

## Challenges
Node imports and requires are difficult to integrate into an extension. To resolve this, browserify was used to "bundle" node imports into the js files that require them.

Several knowledge API's could not yield a satisfactory response to most queries; They either could not understand the prompt, or provided significant (white noise) facts in their responses. In the end, google factcheck tools was found to provide sufficient results to most claims.

Due to certain required python packages not being possible to install on linux, we were unable to deploy the API to Azure.

Our initial goal was to implement our factual claim detector, ClaimBuster, in python ourselves. Unfortunately, time proved to be a major challenge. We ended up scrapping this idea and using this [API](https://idir.uta.edu/claimbuster/api/) instead.

Auto-generated captions on YouTube do not contain periods. This made it much more difficult to tokenize the captions into sentences.

Google's FactCheck API is still in it's nascent stages and as a result is quite inaccurate. If there are more than one or two words in the query, you will most likely get zero results. To address this we implemented Azure's Key Phrase API to limit our queries to key phrases.

## Accomplishments
- On the event of a webpage tab switch, the extension is able to detect if the webpage is a valid youtube video URL, deactivating/reactivating the extension based on this.

- Polygraph is quite robust, and is able to detect false claims on a wide variety of topics. All detected claims seen in the demo are real detections made by the Polygraph.

## What I learned
- While similar to web apps, chrome extensions use processes and developmental frameworks of their own, which was learned to create this project.

## What's next for Polygraph
- The loading and communication with models and data in external API's causes a significant startup time for the app, and to a lesser extent the actual claim checking itself. Further work can be done to understand and implement the processes locally to speed up the process.

- While the algorithms to detect and flag fraudulent claims, accuracy is still quite low. By spending more time to enhance the fact checker, we would be able to flag a significantly higher portion of dubious claims.

- Polygraph is biased towards claims earlier on in the video. By prioritizing claims in a more random fashion we could remove this bias.

- In the future, Polygraph will expand to more social media platforms like Facebook, Twitter, and more


## Sources
https://idir.uta.edu/claimbuster/api/

# notes
Several js files use require; in order for them to be incorporated into the extension, they have to be "bundled" using browserify.

1. `npm install browserify`
2. `browserify FILE.js > bundled\FILE.js`
3. use bundled version of the file in html.

Find the backend [here](https://github.com/alexz429/polygraph)
