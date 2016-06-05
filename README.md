#Website Performance Optimization
Udacity course "Website Performance Optimization"

##Lesson 0: Getting up and running
###Resources
####Course materials
Tips and tricks :https://www.udacity.com/wiki/ud884


###Tools used
* ngrok: https://ngrok.com/
* PageSpeed Insights: https://developers.google.com/speed/pagespeed/insights/
* Chrome Canary
* Remote debugging of Android with Chrome: https://developer.chrome.com/devtools/docs/remote-debugging


####Website Performance Optimization portfolio project
* https://github.com/udacity/frontend-nanodegree-mobile-portfolio
  * python -m SimpleHTTPServer 8080
  * `ngrok http 8080`


##Lesson 1: The Critical Rendering Path
###The DOM
* https://developers.google.com/web/fundamentals/performance/critical-rendering-path/
* Optimizing for performance is all about understanding what happens in these intermediate steps between receiving the HTML, CSS, and JavaScript bytes and the required processing to turn them into rendered pixels - that’s the critical rendering path.
* First, we grab the HTML and start building the DOM. We then fetch the CSS and build the CSS Object Model; we combine those two, in order to render the tree:
DOM ----> CSSOM --> Render tree --> Layout --> Paint
|-JavaScript-|


###Converting HTML to the DOM
* Constructing the DOM: https://developers.google.com/web/fundamentals/performance/critical-rendering-path/constructing-the-object-model#document-object-model-dom
* `curl http://udacity-crp.herokuapp.com/cssom.html`
* What is the DOM: https://www.w3.org/TR/DOM-Level-2-Core/introduction.html
  * The Document Object Model (DOM) is an application programming interface (API) for valid HTML and well-formed XML documents. It defines the logical structure of documents and the way a document is accessed and manipulated.
* Whenever we receive a tag, the browser emits a token.
* When the browser receives the <html> tag, it sends the "StartTag: HTML" token, and so on (e.g. "StartTag:span", "EndTag:span", "Tag:meta", "Tag:link"). That process is done by the tokenizer.
* At the same time, there is another process consuming this tokens and converting them to node objects. There are parent-child relationship between the nodes.
* Eventually, once we consume all the tokens, we arrive to the DOM, which is a tree structure with all the nodes and properties.
* The DOM is the full part representation of the HTML markup


###Fast Google Search Responses
* Incremental HTML delivery
  * Flushing the document early: http://www.stevesouders.com/blog/2009/05/18/flushing-the-document-early/
  * **Flushing** is when the server sends the initial part of the HTML document to the client before the entire response is ready. When done correctly, flushing results in a page that loads and feels faster. 
  * The key is choosing the right point at which to flush the partial HTML document response. The flush should occur before the expensive parts of the back end work, such as database queries and web service calls. But the flush should occur after the initial response has enough content to keep the browser busy. The part of the HTML document that is flushed should contain some resources as well as some visible content. If resources (e.g., stylesheets, external scripts, and images) are included, the browser gets an early start on its download work. If some visible content is included, the user receives feedback sooner that the page is loading.
* Check out Chunk Scatter, a fantastic tool for visualizing chunked HTTP responses.
  * http://blog.cowchimp.com/chunk-scatter-http-chunked-response-analysis-tool/
  * http://scatter.cowchimp.com/


###Exploring Timeline Traces
* https://developer.chrome.com/devtools/docs/timeline
* DevTools emulation docs: https://developer.chrome.com/devtools/docs/device-mode


###Converting CSS to CSSOM
* The CSS are converted to tokens, and those to nodes and a DOM
  * The children of the node inherit the uppder styles.
* Why and how CSS blocks rendering: https://developers.google.com/web/fundamentals/performance/critical-rendering-path/render-blocking-css
* By default CSS is treated as a render blocking resource, which means that the browser will hold rendering of any processed content zntil the CSSOM is constructed. Make sure to keep your CSS lean, deliver it as quickly as possible, and use media types and queries to unblock rendering.
* The critical rendering path requires that we have both the DOM and the CSSOM to construct the render tree, which creates an important performance implication: **both HTML and CSS are render blocking resources**.
* Media types and media queries allow us to mark some CSS resources as non-render blocking.
* CSS is a render blocking resource, get it down to the client as soon and as quickly as possible to optimize the time to first render!
* When declaring your stylesheet assets, pay close attention to the media type and queries, as they will have big performance impact on the critical rendering path!
* A page without CSS:  “Flash of Unstyled Content” (FOUC)
* Why does the browser match CSS selectors from right to left?
  * http://stackoverflow.com/questions/5797014/why-do-browsers-match-css-selectors-from-right-to-left
* A more specific CSS rule is more expensive because it has to traverse more nodes.


###Recalculating CSS Styles in DevTools
* Saving and loading recordings
  * https://developer.chrome.com/devtools/docs/timeline#saving-and-loading-recordings


###The render tree
* Demo page: http://udacity-crp.herokuapp.com/cssom.html
* https://developers.google.com/web/fundamentals/performance/critical-rendering-path/render-tree-construction
* It only captures visible content.
* The CSSOM and DOM trees are combined into a render tree, which is then used to compute the layout of each visible element and serves as an input to the paint process which renders the pixels to screen. 


###Layout
* Always use the meta tag "viewport": `<meta name="viewport" content="width=device-width">`
* Layout computes the exact position and size of each object.
* Responsive web design basics: https://developers.google.com/web/fundamentals/design-and-ui/responsive/fundamentals/


###Paint
* Paint is the last step that takes in the final render tree and renders the pixels to the screen.
* We can finally pass this information to our final stage which will convert each node in the render tree to actual pixels on the screen - this step is often referred to as “painting” or “rasterizing.”


###Analyzing Layout in DevTools
* Layout can be triggered by device orientation change on mobile, a window resize, or any other action that modifies the content of the DOM - e.g. adding or removing content from the DOM tree, toggling CSSOM properties on a node, and so on!
* Expensive Layouts post: https://discussions.udacity.com/t/expensive-layouts/16165?_ga=1.152352697.1474336584.1464813511

###Recap
Let’s do a quick recap of all the steps the browser went through:
1. Process HTML markup and build the **DOM tree**.
2. Process CSS markup and build the **CSSOM tree**.
3. Combine the DOM and CSSOM into a **render tree**.
4. Run **layout** on the render tree to compute geometry of each node.
5. **Paint** the individual nodes to the screen.
* Optimizing the critical rendering path is the process of minimizing the total amount of time spent in steps 1 through 5 in the above sequence.
* Share a timeline link: http://www.webpagetest.org/

* Process for rendering a page:
1. Begin constructing a DOM by parsing HTML.
2. Request CSS and JS resources.
3. Parse CSS and construct CSSOMt tree.
4. Execute JS.
5. Merge DOM and CSSOM into the Render Tree.
6. Run layout, paint.

* Link to Optimization Suggestions forum discussion: 
https://discussions.udacity.com/t/optimization-suggestions/16163?_ga=1.145005748.1474336584.1464813511


##Lesson 2: Optimizing the CRP
###Optimizing the DOM


##Interesting links
* https://developers.google.com/web/fundamentals/performance/rendering/?hl=en