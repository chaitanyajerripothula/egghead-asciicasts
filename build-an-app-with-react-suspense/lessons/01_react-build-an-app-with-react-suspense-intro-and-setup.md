Chantastic: [0:00] What's up friends, I'm Chantastic and I'm going to help you build an app with React suspense hooks in Context. It's going to be awesome. We're going to have a lot of fun.

[0:12] I want to show you how to have the most amount of success in this course, make sure that you know everything that you need to know to be successful. Let's dive in.

[0:25] First thing, React JS docs are amazing, use them. Second of all, you need to know where to find the concurrent docs. If you go to reactjs.org, you should have been there a million times by now. We're going to type in, "Concurrent." /concurrent, that's where all of the concurrent suspense docs live.

[0:49] This is where we're going to live. Number two, second of all, you see this big red caution thing and a big, super bold, super big experimental on this. That cannot be overstated enough. Everything that we're learning in this workshop is experimental.

[1:13] What that means is not that it's bad or that it's not fully baked or flushed out. It just means that some of these things may be subject to change, removal, or whatnot.

[1:26] We've seen totally different React Suspense APIs announced a year ago. I think that these are absolutely going to stick around, but the React team makes no promises. Things could be removed, renamed, added to. You need to know that going into this workshop, things could change.

[1:53] As things change, how do you stay up to date? Good question. Everything that we are learning is going to be happening inside of this GitHub project repository. You can find it at github.com/chantastic/react-suspense-course.

[2:16] All of the lessons that we go through are in this repository. In fact, you can go through the lessons without the videos.

[2:25] You wouldn't want to do that because the videos are extremely helpful. There's a lot of nuance there, but all of the exercises in this repository. Where are they? They're going to be in src.

[2:40] You're going to go into Lessons. Each video connects to one of these lesson numbers.

[2:47] Let's go to 101. We have our app.js, that's everything that you need to start this lesson. The pokemon-detail...I did just lie, you need both of these files, and there's a readme here. The readme has your exercise, what you're going to be doing and the steps to accomplish that.

[3:11] In the videos, we go through all of this, but this is the text version of that. Video connects to directory in the lesson's directory of this React Suspense course repository. So many things.

[3:30] How do we get this project on your computer? Let's do that right now. We're going to copy this URL, `git clone`, copy that.
```bash
git clone https://github.com/chantastic/react-suspense-course.git
```

[3:48] Be pretty quick, but then we got to do something that's not super quick. We're going to say `yarn` and `cd` into React Suspense course. `yarn`. This one is going to take a minute. I will fast forward right now for your convenience.

```bash
yarn

cd react-suspense-course

yarn
 ```

[4:04] Now that this is ready to go, we can say Yarn...Actually, let's open it up in our code editor. I'm going to be using VS Code for all of the videos, that's what I'm using, defaults. Default theme and all that stuff.

```bash
code .
```

[4:20] To go back to our terminal, we can say `yarn start`, that's going to start the project and open up in a browser for us, which is nice.

[4:31] This whole thing uses create-react-app. If you're familiar with create-react-app, you know everything that you need to know to run this project. You will see this completed version of the application, which is pretty neat. I want to show you how to jump around between lessons.

[4:55] With our app running, let's go into source index.js. This is going to be an important file for us, source index.js. This is one where we change from legacy mode to concurrent mode. That's important in one of the lessons, but more so than that, this is where we change between lessons.

[5:16] Right now, I'm on the complete lesson, but I can jump to 101, which is the first lesson. It'll automatically reload into that state. 

```js
import React from react";
import ReactDOM from "react-dom";
import App from "./lessons/101/app";
```

One of the later lessons is 118.

[5:30] Let's jump to that one. You can see all that suspensey stuff coming in. This is going to be about where we end the workshop videos.

```js
import React from react";
import ReactDOM from "react-dom";
import App from "./lessons/118/app";
```

[5:46] One of the benefits of keeping it unstyled is that at no point is this app more than 300 lines, which means you could recreate it from memory, which is awesome. We get that by keeping the components really small during all of the lessons.

[6:03] However, you just saw that if you want to go to that complete version, you can just eat your heart out on the styles. That is totally styled and has some fun little extras in there if you want to play with that. Complete, and then all the code is right in there.

```js
import React from react";
import ReactDOM from "react-dom";
import App from "./lessons/complete/app";
```

[6:27] That's about everything that you need to know. One last thing that'll be helpful is that we're using these new React channels. If we go to the {React blog](https://react.js/blog) and we go here, we can see that they have published channels.

[6:46] Because all of this stuff is experimental, it's not going to be in the default published npm build of React. They're guarding it behind this experimental channel.

[6:56] If you go all the way to the bottom here, you can see how to install these channel versions and see what the experimental channel is all about. In our case, if we wanted to, we could install those by doing `yarn add react@experimental` and `react-dom@experimental`.

```bash
yarn add react@experimental react-dom@experimental
```

[7:19] What that'll do is that experimental alias is going to grab the latest and greatest experimental version. If you've cloned the repository, that is the version that these lessons are built on. Everything will work if you just keep it on the version that's there, but this is how you do it in app if you want to go your own path.

[7:43] That's it. I am super thrilled that you are learning React Suspense with me. We're going to have a good time.

[7:52] I am always available to help you out should any questions arise. Good luck. Godspeed. Have a lot of fun.
