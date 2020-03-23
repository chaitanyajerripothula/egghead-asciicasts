Kent C. Dodds: [00:00] What is up, folks? My name is Kent C. Dodds. I am super-excited to be your teacher here on egghead.io, where I'm going to take you through the Beginner's Guide to React JS. We're going to start from the very, very beginning.

[00:11] Whether you've been doing React for a long time, you just want to sharpen yourself on the fundamentals, or you've never done any React before and you're just curious, this is going to be a perfect place for you because we're going to start with a totally blank slate.

[00:24] When we're using React, we're using a bunch of other tools in the ecosystem around it. You've got a router. You've got state management. You've got the building tools like Webpack or Parcel or whatever it is that you're using.

[00:35] When you're using all these tools, it can be pretty difficult to isolate what is React and what is it doing for me. If you don't isolate it effectively, then you can't use it very effectively.

[00:46] We start out with a literal blank index.html file. We write some HTML in it. We write some JavaScript directly in that HTML file. Then, poof. All of a sudden, we're writing some JavaScript to create DOM nodes. We're inserting those DOM nodes into the page. We're making it dynamic. It's awesome.

[01:03] Then we switch over from regular JavaScript to React. You get a really good sense of what piece of this puzzle is React solving and how can I use it most effectively in building applications.

[01:15] Then we upgrade to JSX. I show you some tricks with JSX. Then we go all the way through forms and HTTP, error handling, all of that stuff.

[01:25] You're going to love this. When we get down to the very end, we're going to use some really awesome tools like CodeSandbox and GitHub and Netlify to deploy our application to the world.

[01:37] Then you can show it to your friends and family and your dog and whoever it is that you want to show this to. You'll be so excited that you've built something that is available on the World Wide Web with React. It's super, super cool.

[01:50] Let's go ahead. We'll dive into the workshop stuff so that you can see where the files are, how to get things up and running if you want to follow along or if you just want to tinker with things as we go. Then we can get into this awesome workshop that I've prepared for you.

[02:06] The workshop is available to you on GitHub. You'll find it on my GitHub, kentcdodds/beginners-guide-to-react. I recommend that you switch the branch to Egghead. That way, you make sure that the stuff that you're working with is exactly the same stuff that I recorded in the course.

[02:22] If we make any changes in the future, that will happen on the master branch. The Egghead branch will always be exactly what you see me do in the course. The course is just a bunch of HTML files plus this start script that we'll take a look at. You can download this using Git if you're comfortable with that, or you can just click download ZIP.

[02:41] That will download the ZIP file to your computer. You can open that up and extract that. This is what you're going to find at the end of the day. I am using VS Code. You can feel free to use Sublime or Text Editor or whatever it is that you want to.

[02:56] I recommend VS Code. It's great. We've got each one of the lessons right here as HTML files that you can go through.

[03:04] Th]e HTML file that's available here is the finished version of our code. You can work through each one of these after watching the video, tinker around with it a little bit. To get things up and going, you can actually just open this up. We can click on reveal in Finder here. Then we can double-click on that. It'll just open it right up in the browser for you.

[03:27] That will work for lots of these, but some of these lessons actually require that you're running a real server. There are a couple ways that you can do that if you can figure out how to make that work yourself. I have this start script. That's what I'm using.

[03:41] We're using a tool called Browsersync. We're using npx to get that running. npx comes bundled with Node. If you Node.js installed, then you can use npx. You can just copy and paste this in a terminal window. That will get Browsersync up and running. Then you can open up localhost:3000, where that will show you a listing of all the files.

[04:01] We'll go ahead and click on this first one. Typically, in the workshop, I've got this open up side by side with my code editor. You're going to have it look a little bit like this.

[04:14] Here, you'll notice that I can hit save key. It automatically reloads. We'll also want to have our developer tools open. I've got our developer tools right here. I have that up here at the bottom.

[04:27] We have two scripts in here that you don't find in the source code. That's added by Browsersync. The cool thing about Browsersync is that you don't have to manually hit the reload button. You just go ahead and change something. Hit save. It will automatically reload for you.

[04:45] That's why we're going with Browsersync. It's really nice for this.

[04:49] You can feel free to play around with this stuff as we go. You can clear this stuff out and type it out yourself, or you can just wait until I go through the material. Then you tinker around with the finished product. Or you could just watch through the whole thing, take notes, and then go through it again later on your own.

[05:07] Whatever works best for you. I do recommend that the first time you go through this, you just watch and take notes. That typically ends up in a better experience for most people. You do it however you want to.

[05:19] I can't tell you how excited I am for you to go through this. I've worked really hard to try to make this as approachable as possible, give you really deep foundational understanding without getting too much in the weeds.

[05:30] I can't wait to see the cool things that you build with this knowledge that you acquire through going through this material.

[05:36] Please do let me know what you build and what you create with this knowledge of React. I can't wait for you to experience this. Let's go ahead and jump into the course.