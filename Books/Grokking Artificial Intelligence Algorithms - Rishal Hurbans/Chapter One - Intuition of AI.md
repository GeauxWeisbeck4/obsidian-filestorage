# 1 Intuition of artificial intelligence

This chapter covers

- Definition of AI as we know it
    
- Intuition of concepts that are applicable to AI
    
- Problem types in computer science and AI, and their properties
    
- Overview of the AI algorithms discussed in this book
    
- Real-world uses for AI
    

## What is artificial intelligence?

Intelligence is a mystery—a concept that has no agreed-upon definition. Philosophers, psychologists, scientists, and engineers all have different opinions about what it is and how it emerges. We see intelligence in nature around us, such as groups of living creatures working together, and we see intelligence in the way that humans think and behave. In general, things that are autonomous yet adaptive are considered to be intelligent. _Autonomous_ means that something does not need to be provided constant instructions; and _adaptive_ means that it can change its behavior as the environment or problem space changes. When we look at living organisms and machines, we see that the core element for operation is data. Visuals that we see are data; sounds that we hear are data; measurements of the things around us are data. We consume data, process it all, and make decisions based on it; so a fundamental understanding of the concepts surrounding data is important for understanding artificial intelligence (AI) algorithms.

### Defining AI

Some people argue that we don’t understand what AI is because we struggle to define intelligence itself. Salvador Dalí believed that ambition is an attribute of intelligence; he said, “Intelligence without ambition is a bird without wings.” Albert Einstein believed that imagination is a big factor in intelligence; he said, “The true sign of intelligence is not knowledge, but imagination.” And Stephen Hawking said, “Intelligence is the ability to adapt,” which focuses on being able to adapt to changes in the world. These three great minds had different outlooks on intelligence. With no true definitive answer to intelligence yet, we at least know that we base our understanding of intelligence on humans as being the dominant (and most intelligent) species.

For the sake of our sanity, and to stick to the practical applications in this book, we will loosely define AI as a synthetic system that exhibits “intelligent” behavior. Instead of trying to define something as AI or not AI, let’s refer to the AI-likeness of it. Something might exhibit some aspects of intelligence because it helps us solve hard problems and provides value and utility. Usually, AI implementations that simulate vision, hearing, and other natural senses are seen to be AI-like. Solutions that are able to learn autonomously while adapting to new data and environments are also seen to be AI-likeness.

Here are some examples of things that exhibit AI-likeness:

- A system that succeeds at playing many types of complex games
    
- A cancer tumor detection system
    
- A system that generates artwork based on little input
    
- A self-driving car
    

Douglas Hofstadter said, “AI is whatever hasn’t been done yet.” In the examples just mentioned, a self-driving car may seem to be intelligent because it hasn’t been perfected yet. Similarly, a computer that adds numbers was seen to be intelligent a while ago but is taken for granted now.

The bottom line is that _AI_ is an ambiguous term that means different things to different people, industries, and disciplines. The algorithms in this book have been classified as AI algorithms in the past or present; whether they enable a specific definition of AI or not doesn’t really matter. What matters is that they are useful for solving hard problems.

### Understanding that data is core to AI algorithms

Data is the input to the wonderful algorithms that perform feats that almost appear to be magic. With the incorrect choice of data, poorly represented data, or missing data, algorithms perform poorly, so the outcome is only as good as the data provided. The world is filled with data, and that data exists in forms we can’t even sense. Data can represent values that are measured numerically, such as the current temperature in the Arctic, the number of fish in a pond, or your current age in days. All these examples involve capturing accurate numeric values based on facts. It’s difficult to misinterpret this data. The temperature at a specific location at a specific point in time is absolutely true and is not subject to any bias. This type of data is known as _quantitative data_.

Data can also represent values of observations, such as the smell of a flower or one’s level of agreement with a politician’s policies. This type of data is known as _qualitative data_ and is sometimes difficult to interpret because it’s not an absolute truth, but a perception of someone’s truth. Figure 1.1 illustrates some examples of the quantitative and qualitative data around us.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617296185/files/OEBPS/Images/CH01_F01_Hurbans.png)

Figure 1.1 Examples of data around us

Data is raw facts about things, so recordings of it usually have no bias. In the real world, however, data is collected, recorded, and related by people based on a specific context with a specific understanding of how the data may be used. The act of constructing meaningful insights to answer questions based on data is creating _information_. Furthermore, the act of utilizing information with experiences and consciously applying it creates _knowledge_. This is partly what we try to simulate with AI algorithms.

Figure 1.2 shows how quantitative and qualitative data can be interpreted. Standardized instruments such as clocks, calculators, and scales are usually used to measure quantitative data, whereas our senses of smell, sound, taste, touch, and sight, as well as our opinionated thoughts, are usually used to create qualitative data.

![A screenshot of a cell phone Description automatically generated](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617296185/files/OEBPS/Images/CH01_F02_Hurbans.png)

Figure 1.2 Qualitative data versus quantitative data

Data, information, and knowledge can be interpreted differently by different people, based on their level of understanding of that domain and their outlook on the world, and this fact has consequences for the quality of solutions—making the scientific aspect of creating technology hugely important. By following repeatable scientific processes to capture data, conduct experiments, and accurately report findings, we can ensure more accurate results and better solutions to problems when processing data with algorithms.

### Viewing algorithms as instructions in recipes

We now have a loose definition of AI and an understanding of the importance of data. Because we will be exploring several AI algorithms throughout this book, it is useful to understand exactly what an algorithm is. An _algorithm_ is a set of instructions and rules provided as a specification to accomplish a specific goal. Algorithms typically accept inputs, and after several finite steps in which the algorithm progresses through varying states, an output is produced.

Even something as simple as reading a book can be represented as an algorithm. Here’s an example of the steps involved in reading this book:

1. Find the book _Grokking Artificial Intelligence Algorithms_.
    
2. Open the book.
    
3. While unread pages remain,
    
    1. Read page.
        
    2. Turn to next page.
        
    3. Think about what you have learned.
        
4. Think about how you can apply your learnings in the real world.
    

An algorithm can be viewed as a recipe, as seen in figure 1.3. Given some ingredients and tools as inputs, and instructions for creating a specific dish, a meal is the output.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617296185/files/OEBPS/Images/CH01_F03_Hurbans.png)

Figure 1.3 An example showing that an algorithm is like a recipe

Algorithms are used for many different solutions. For example, we can enable live video chat across the world through compression algorithms, and we can navigate cities through map applications that use real-time routing algorithms. Even a simple “Hello World” program has many algorithms at play to translate the human-readable programming language into machine code and execute the instructions on the hardware. You can find algorithms everywhere if you look closely enough.

To illustrate something more closely related to the algorithms in this book, figure 1.4 shows a number-guessing-game algorithm represented as a flow chart. The computer generates a random number in a given range, and the player attempts to guess that number. Notice that the algorithm has discrete steps that perform an action or determine a decision before moving to the next operation.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617296185/files/OEBPS/Images/CH01_F04_Hurbans.png)

Figure 1.4 A number-guessing-game algorithm flow chart

Given our understanding of technology, data, intelligence, and algorithms: AI algorithms are sets of instructions that use data to create systems that exhibit intelligent behavior and solve hard problems.

## A brief history of artificial intelligence

A brief look back at the strides made in AI is useful for understanding that old techniques and new ideas can be harnessed to solve problems in innovative ways. AI is not a new idea. History is filled with myths of mechanical men and autonomous “thinking” machines. Looking back, we find that we’re standing on the shoulders of giants. Perhaps we ourselves can contribute to the pool of knowledge in a small way.

Looking at past developments highlights the importance of understanding the fundamentals of AI; algorithms from decades ago are critical in many modern AI implementations. This book starts with fundamental algorithms that help build the intuition of problem-solving and gradually moves to more interesting and modern approaches.

Figure 1.5 isn’t an exhaustive list of achievements in AI—it is simply a small set of examples. History is filled with many more breakthroughs!

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617296185/files/OEBPS/Images/CH01_F05_Hurbans.png)

Figure 1.5 The evolution of AI

## Problem types and problem-solving paradigms

AI algorithms are powerful, but they are not silver bullets that can solve any problem. But what are problems? This section looks at different types of problems that we usually experience in computer science, showing how we can start gaining intuition about them. This intuition can help us identify these problems in the real world and guide the choice of algorithms used in the solution.

Several terms in computer science and AI are used to describe problems. Problems are classified based on the _context_ and the _goal_.

### Search problems: Find a path to a solution

A _search problem_ involves a situation that has multiple possible solutions, each of which represents a sequence of steps (path) toward a goal. Some solutions contain overlapping subsets of paths; some are better than others; and some are cheaper to achieve than others. A “better” solution is determined by the specific problem at hand; a “cheaper” solution means computationally cheaper to execute. An example is determining the shortest path between cities on a map. Many routes may be available, with different distances and traffic conditions, but some routes are better than others. Many AI algorithms are based on the concept of searching a solution space.

### Optimization problems: Find a good solution

An _optimization problem_ involves a situation in which there are a vast number of valid solutions and the absolute-best solution is difficult to find. Optimization problems usually have an enormous number of possibilities, each of which differs in how well it solves the problem. An example is packing luggage in the trunk of a car in such a way as to maximize the use of space. Many combinations are available, and if the trunk is packed effectively, more luggage can fit in it.

Local best versus global best

Because optimization problems have many solutions, and because these solutions exist at different points in the search space, the concept of local bests and global bests comes into play. A _local best_ solution is the best solution within a specific area in the search space, and a _global best_ is the best solution in the entire search space. Usually, there are many local best solutions and one global best solution. Consider searching for the best restaurant, for example. You may find the best restaurant in your local area, but it may not necessarily be the best restaurant in the country or the best restaurant in the world.

### Prediction and classification problems: Learn from patterns in data

_Prediction_ _problems_ are problems in which we have data about something and want to try to find patterns. For example, we might have data about different vehicles and their engine sizes, as well as each vehicle’s fuel consumption. Can we predict the fuel consumption of a new model of vehicle, given its engine size? If there’s a correlation in the data between engine sizes and fuel consumption, this prediction is possible.

_Classification problems_ are similar to prediction problems, but instead of trying to find an exact prediction such as fuel consumption, we try to find a category of something based on its features. Given the dimensions of a vehicle, its engine size, and the number of seats, can we predict whether that vehicle is a motorcycle, sedan, or sport-utility vehicle? Classification problems require finding patterns in the data that group examples into categories. Interpolation is an important concept when finding patterns in data because we estimate new data points based on the known data.

### Clustering problems: Identify patterns in data

_Clustering_ _problems_ include scenarios in which trends and relationships are uncovered from data. Different aspects of the data are used to group examples in different ways. Given cost and location data about restaurants, for example, we may find that younger people tend to frequent locations where the food is cheaper.

Clustering aims to find relationships in data even when a precise question is not being asked. This approach is also useful for gaining a better understanding of data to inform what you might be able to do with it.

### Deterministic models: Same result each time it’s calculated

_Deterministic_ _models_ are models that, given a specific input, return a consistent output. Given the time as noon in a specific city, for example, we can always expect there to be daylight; and given the time as midnight, we can always expect darkness. Obviously this simple example doesn’t take into account the unusual daylight durations near the poles of the planet.

### Stochastic/probabilistic models: Potentially different result each time it’s calculated

_Probabilistic_ _models_ are models that, given a specific input, return an outcome from a set of possible outcomes. Probabilistic models usually have an element of controlled randomness that contributes to the possible set of outcomes. Given the time as noon, for example, we can expect the weather to be sunny, cloudy, or rainy; there is no fixed weather at this time.

## Intuition of artificial intelligence concepts

AI is a hot topic, as are machine learning and deep learning. Trying to make sense of these different but similar concepts can be a daunting experience. Additionally, within the domain of AI, distinctions exist among different levels of intelligence.

In this section, we demystify some of these concepts. The section is also a road map of the topics covered throughout this book.

Let’s dive into the different levels of AI, introduced with figure 1.6.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617296185/files/OEBPS/Images/CH01_F06_Hurbans.png)

Figure 1.6 Levels of AI

### Narrow intelligence: Specific-purpose solutions

_Narrow_ _intelligence_ systems solve problems in a specific context or domain. These systems usually cannot solve a problem in one context and apply that same understanding in another. A system developed to understand customer interactions and spending behavior, for example, would not be capable of identifying cats in an image. Usually, for something to be effective in solving a problem, it needs to be quite specialized in the domain of the problem, which makes it difficult to adapt to other problems.

Different narrow intelligence systems can be combined in sensible ways to create something greater that seems to be more general in its intelligence. An example is a voice assistant. This system can understand natural language, which alone is a narrow problem, but through integration with other narrow intelligence systems, such as web searches and music recommenders, it can exhibit qualities of general intelligence.

### General intelligence: Humanlike solutions

_General_ _intelligence_ is humanlike intelligence. As humans, we are able to learn from various experiences and interactions in the world and apply that understanding from one problem to another. If you felt pain when touching something hot as a child, for example, you can extrapolate and know that other things that are hot may have a chance of hurting you. General intelligence in humans, however, is more than just reasoning something like “Hot things may be harmful.” General intelligence encompasses memory, spatial reasoning through visual inputs, use of knowledge, and more. Achieving general intelligence in a machine seems to be an unlikely feat in the short term, but advancements in quantum computing, data processing, and AI algorithms could make it a reality in the future.

### Super intelligence: The great unknown

Some ideas of _super intelligence_ appear in science-fiction movies set in postapocalyptic worlds, in which all machines are connected, are able to reason about things beyond our understanding, and dominate humans. There are many philosophical differences about whether humans could create something more intelligent than ourselves and, if we could, whether we’d even know. Super intelligence is the great unknown, and for a long time, any definitions will be speculation.

### Old AI and new AI

Sometimes, the notions of old AI and new AI are used. _Old AI_ is often understood as being systems in which people encoded the rules that cause an algorithm to exhibit intelligent behavior—via in-depth knowledge of the problem or by trial and error. An example of old AI is a person manually creating a decision tree and the rules and options in the entire decision tree. _New AI_ aims to create algorithms and models that learn from data and create their own rules that perform as accurately as, or better than, human-created rules. The difference is that the latter may find important patterns in the data that a person may never find or that would take a person much longer to find. Search algorithms are often seen as old AI, but a robust understanding of them is useful in learning more complex approaches. This book aims to introduce the most popular AI algorithms and gradually build on each concept. Figure 1.7 illustrates the relationship between some of the different concepts within artificial intelligence.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617296185/files/OEBPS/Images/CH01_F07_Hurbans.png)

Figure 1.7 Categorization of concepts within AI

### Search algorithms

_Search_ _algorithms_ are useful for solving problems in which several actions are required to achieve a goal, such as finding a path through a maze or determining the best move to make in a game. Search algorithms evaluate future states and attempt to find the optimal path to the most valuable goal. Typically, we have too many possible solutions to brute-force each one. Even small search spaces could result in thousands of hours of computing to find the best solution. Search algorithms provide smart ways to evaluate the search space. Search algorithms are used in online search engines, map routing applications, and even game-playing agents.

### Biology-inspired algorithms

When we look at the world around us, we notice incredible things in various creatures, plants, and other living organisms. Examples include the cooperation of ants in gathering food, the flocking of birds when migrating, estimating how brains work, and the evolution of different organisms to produce stronger offspring. By observing and learning from various phenomena, we’ve gained knowledge of how these organic systems operate and of how simple rules can result in emergent intelligent behavior. Some of these phenomena have inspired algorithms that are useful in AI, such as evolutionary algorithms and swarm intelligence algorithms.

_Evolutionary algorithms_ are inspired by the theory of evolution defined by Charles Darwin. The concept is that a population reproduces to create new individuals and that through this process, the mixture of genes and mutation produces individuals that perform better than their ancestors. _Swarm intelligence_ is a group of seemingly “dumb” individuals exhibiting intelligent behavior. Ant-colony optimization and particle-swarm optimization are two popular algorithms that we will be exploring in this book.

### Machine learning algorithms

Machine learning takes a statistical approach to training models to learn from data. The umbrella of machine learning has a variety of algorithms that can be harnessed to improve understanding of relationships in data, to make decisions, and to make predictions based on that data.

There are three main approaches in machine learning:

- _Supervised learning_ means training models with algorithms when the training data has known outcomes for a question being asked, such as determining the type of fruit if we have a set of data that includes the weight, color, texture, and fruit label for each example.
    
- _Unsupervised learning_ uncovers hidden relationships and structures within the data that guide us in asking the dataset relevant questions. It may find patterns in properties of similar fruits and group them accordingly, which can inform the exact questions we want to ask the data. These core concepts and algorithms helps us create a foundation for exploring advanced algorithms in the future.
    
- _Reinforcement learning_ is inspired by behavioral psychology. In short, it describes rewarding an individual if a useful action was performed and penalizing that individual if an unfavorable action was performed. To explore a human example, when a child achieves good results on their report card, they are usually rewarded, but poor performance sometimes results in punishment, reinforcing the behavior of achieving good results. Reinforcement learning is useful for exploring how computer programs or robots interact with dynamic environments. An example is a robot that is tasked to open doors; it is penalized when it doesn’t open a door and rewarded when it does. Over time, after many attempts, the robot “learns” the sequence of actions required to open a door.
    

### Deep learning algorithms

_Deep_ _learning_, which stems from machine learning, is a broader family of approaches and algorithms that are used to achieve narrow intelligence and strive toward general intelligence. Deep learning usually implies that the approach is attempting to solve a problem in a more general way like spatial reasoning, or it is being applied to problems that require more generalization such as computer vision and speech recognition. General problems are things humans are good at solving. For example, we can match visual patterns in almost any context. Deep learning also concerns itself with supervised learning, unsupervised learning, and reinforcement learning. Deep learning approaches usually employ many layers of artificial neural networks. By leveraging different layers of intelligent components, each layer solves specialized problems; together, the layers solve complex problems toward a greater goal. Identifying any object in an image, for example, is a general problem, but it can be broken into understanding color, recognizing shapes of objects, and identifying relationships among objects to achieve a goal.

## Uses for artificial intelligence algorithms

The uses for AI techniques are potentially endless. Where there are data and problems to solve, there are potential applications of AI. Given the ever-changing environment, the evolution of interactions among humans, and the changes in what people and industries demand, AI can be applied in innovative ways to solve real-world problems. This section describes the application of AI in various industries.

### Agriculture: Optimal plant growth

One of the most important industries that sustain human life is agriculture. We need to be able to grow quality crops for mass consumption economically. Many farmers grow crops on a commercial scale to enable us to purchase fruit and vegetables at stores conveniently. Crops grow differently based on the type of crop, the nutrients in the soil, the water content of the soil, the bacteria in the water, and the weather conditions in the area, among other things. The goal is to grow as much high-quality produce as possible within a season, because specific crops generally grow well only during specific seasons.

Farmers and other agriculture organizations have captured data about their farms and crops over the years. Using that data, we can leverage machines to find patterns and relationships among the variables in the crop-growing process and identify the factors that contribute most to successful growth. Furthermore, with modern digital sensors, we can record weather conditions, soil attributes, water conditions, and crop growth in real time. This data, combined with intelligent algorithms, can enable real-time recommendations and adjustments for optimal growth (figure 1.8).

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617296185/files/OEBPS/Images/CH01_F08_Hurbans.png)

Figure 1.8 Using data to optimize crop farming

### Banking: Fraud detection

The need for banking became obvious when we had to find a common consistent currency for trading goods and services. Banks have changed over the years to offer different options for storing money, investing money, and making payments. One thing that hasn’t changed over time is people finding creative ways to cheat the system. One of the biggest problems—not only in banking but also in most financial institutions, such as insurance companies—is fraud. _Fraud_ occurs when someone is dishonest or does something illegal to acquire something for themselves. Fraud usually happens when loopholes in a process are exploited or a scam fools someone into divulging information. Because the financial-services industry is highly connected through the internet and personal devices, more transactions happen electronically over a computer network than in person, with physical money. With the vast amounts of transaction data available, we can, in real-time, find patterns of transactions specific to an individual’s spending behavior that may be out of the ordinary. This data helps save financial institutions enormous amounts of money and protects unsuspecting consumers from being robbed.

### Cybersecurity: Attack detection and handling

One of the interesting side effects of the internet boom is cybersecurity. We send and receive sensitive information over the internet all the time—instant messages, credit-card details, emails, and other important confidential information that could be misused if it fell into the wrong hands. Thousands of servers across the globe receive data, process it, and store it. Attackers attempt to compromise these systems to gain access to the data, devices, or even facilities.

By using AI, we can identify and block potential attacks on servers. Some large internet companies store data about how specific individuals interact with their service, including their device IDs, geolocations, and usage behavior; when unusual behavior is detected, security measures limit access. Some internet companies can also block and redirect malicious traffic during a distributed denial of service (DDoS) attack, which involves overloading a service with bogus requests in an attempt to bring it down or prevent access by authentic users. These unauthentic requests can be identified and rerouted to minimize the impact of the attack by understanding the users’ usage data, the systems, and the network.

### Health care: Diagnosis of patients

Health care has been a constant concern throughout human history. We need access to diagnosis and treatment of different ailments in different locations in varying windows of time before a problem becomes more severe or even fatal. When we look at the diagnosis of a patient, we may look at the vast amounts of knowledge recorded about the human body, known problems, experience in dealing with these problems, and a myriad of scans of the body. Traditionally, doctors were required to analyze images of scans to detect the presence of tumors, but this approach resulted in the detection of only the largest, most advanced tumors. Advances in deep learning have improved the detection of tumors in images generated by scans. Now doctors can detect cancer earlier, which means that a patient can get the required treatment in time and have a higher chance of recovery.

Furthermore, AI can be used to find patterns in symptoms, ailments, hereditary genes, geographic locations, and the like. We could potentially know that someone has a high probability of developing a specific ailment and be prepared to manage that ailment before it develops. Figure 1.9 illustrates feature recognition of a brain scan using deep learning.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617296185/files/OEBPS/Images/CH01_F09_Hurbans.png)

Figure 1.9 Using machine learning for feature recognition in brain scans

### Logistics: Routing and optimization

The logistics industry is a huge market of different types of vehicles delivering different types of goods to different locations, with different demands and deadlines. Imagine the complexity in a large e-commerce site’s delivery planning. Whether the deliverables are consumer goods, construction equipment, parts for machinery, or fuel, the system aims to be as optimal as possible to ensure that demand is met and costs are minimized.

You may have heard of the traveling-salesperson problem: a salesperson needs to visit several locations to complete their job, and the aim is to find the shortest distance to accomplish this task. Logistics problems are similar but usually immensely more complex due to the changing environment of the real world. Through AI, we can find optimal routes between locations in terms of time and distance. Furthermore, we can find the best routes based on traffic patterns, construction blockages, and even road types based on the vehicle being used. Additionally, we can compute the best way to pack each vehicle and what to pack in each vehicle in such a way that each delivery is optimized.

### Telecoms: Optimizing networks

The telecommunications industry has played a huge role in connecting the world. These companies lay expensive infrastructure of cables, towers, and satellites to create a network that many consumers and organizations can use to communicate via the internet or private networks. Operating this equipment is expensive, so optimization of a network allows for more connections, which allows more people to access high-speed connections. AI can be used to monitor behavior on a network and optimize routing. Additionally, these networks record requests and responses; this data can be used to optimize the networks based on known load from certain individuals, areas, and specific local networks. The network data can also be instrumental for understanding where people are and who they are, which is useful for city planning.

### Games: Creating AI agents

Since home and personal computers first became widely available, games have been a selling point for computer systems. Games were popular very early in the history of personal computers. If we think back, we may remember arcade machines, television consoles, and personal computers with gaming capabilities. The games of chess, backgammon, and others have been dominated by AI machines. If the complexity of a game is low enough, a computer can potentially find all possibilities and make a decision based on that knowledge faster than a human can. Recently, a computer was able to defeat human champions in the strategic game, Go. Go has simple rules for territory control but has huge complexity in terms of the decisions that need to be made for a winning scenario. A computer can’t generate all possibilities for beating the best human players because the search space is so large; instead, it calls for a more-general algorithm that can “think” abstractly, strategize, and plan moves toward a goal. That algorithm has already been invented and has succeeded in defeating world champions. It has also been adapted to other applications, such as playing Atari games and modern multiplayer games. This system is called Alpha Go.

Several research organizations have developed AI systems that are capable of playing highly complex games better than human players and teams. The goal of this work is to create general approaches that can adapt to different contexts. At face value, these game-playing AI algorithms may seem unimportant, but the consequence of developing these systems is that the approach can be applied effectively in other important problem spaces. Figure 1.10 illustrates how a reinforcement learning algorithm can learn to play a classic video game like Mario.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617296185/files/OEBPS/Images/CH01_F10_Hurbans.png)

Figure 1.10 Using neural networks to learn how to play games

### Art: Creating masterpieces

Unique, interesting artists have created beautiful paintings. Each artist has their own way of expressing the world around them. We also have amazing music compositions that are appreciated by the masses. In both cases, the quality of the art cannot be measured quantitatively; rather, it is measured qualitatively (by how much people enjoy the piece). The factors involved are difficult to understand and capture; the concept is driven by emotion.

Many research projects aim to build AI that generates art. The concept involves generalization. An algorithm would need to have a broad and general understanding of the subject to create something that fits those parameters. A Van Gogh AI, for example, would need to understand all of Van Gogh’s work and extract the style and “feel” so that it can apply that data to other images. The same thinking can be applied to extracting hidden patterns in areas such as health care, cybersecurity, and finance.

Now that we have abstract intuition about what AI is, the categorization of themes within it, the problems it aims to solve, and some use cases, we will be diving into one of the oldest and simplest forms of mimicking intelligence: search algorithms. Search algorithms provide a good grounding in some concepts that are employed by other, more sophisticated AI algorithms explored throughout this book.

## Summary of Intuition of artificial intelligence

![[Pasted image 20250301172739.png]]