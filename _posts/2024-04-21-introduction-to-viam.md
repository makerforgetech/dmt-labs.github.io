---
layout: post
title: Build Robots Faster with Viam!
date: 2024-04-27 00:00
categories: [Robotics, Viam, Python] 
image: /assets/img/posts/2024-04-21-introduction-to-viam/thumb.png

---

Viam is a software platform that has been designed to enable faster development of robots and smart machines by providing easy configuration and prototyping, simple sharing of functionality, and a community to support any issues.

This video is sponsored by Viam, but I want to be clear that I approached them to propose a partnership because I believe that their software is a solid approach to leveling up my project, and hopefully other projects in the future.

{% include embed/youtube.html id='WM4f_WrR9QY' %}

Viam works by installing the Viam ‘server’ on your device with a few simple commands, then connecting to the server via their helpful user interface.
Once you’re connected you can configure the components you’re using, such as motors and cameras, then control them directly from the UI, write code to teach them how to interact, or use one of their already existing examples.

As you’re configuring your robot a web-enabled logger will output the status and any debug output which can help if you have any issues.

## Rover Rental

Before you get started on your own device you can rent a rover for free. This is a limited time use of one of the Viam rovers that are configured in the Viam offices. By renting a rover you can try out the features and configuration without needing access to your own robot. You can even program the rovers remotely!


## Getting Started

Getting set up to use Viam is easy. Just create an account at app.viam.com and follow the steps in their installation guide.

## Modules

Viam has a number of existing modular resources that you can leverage to build your robot. This makes the whole process much easier than building from scratch as you just need to add the module, set the configuration and Viam will do the rest.

If you can’t find what you’re looking for, there is also a modular registry that allows community members to publish their own modules for others to use. I already have a few available there myself.
The modules from the registry can be configured as easily as the viam-owned modules, but bear in mind that the quality of these can vary depending on the state of completion and compatibility with your hardware.

## Advantages

So let’s talk about the advantages of using Viam in my project specifically.

The modular biped uses Python for everything except the arduino script. Most of that has been pulled together and modified from example scripts on the internet. Although I have done my best to keep everything in order, there are deviating standards and messy scripts littered throughout the entire codebase.

This makes the project difficult to maintain and difficult for others to learn.

In addition, the configuration of the robot involves installing all the dependencies for all the modules at once, which can often lead to errors and, given that the idea is to make the project modular, can mean people are installing dependencies for modules they will never use.

In contrast, Viam’s configuration process means that each module has it’s own dependency installation step, so if someone isn’t using the module they won’t have to install the dependencies. And if the dependencies for a module changes, the latest version can be published and those dependencies will be managed automatically.

In order to configure the modules I currently have a separate JSON file for the configuration information of each. Again, this makes modification and maintenance difficult (although easier than it used to be!). Viam has a single JSON that can be imported to set everyone up with the same configuration, which can then be modified via the UI as needed. 

This approach means that the set-up of the robot goes from a long list of manual steps, to a few, easy, UI based steps.

I can also start to leverage the modular registry and the growing list of modules that are available there, rather than building everything from scratch. The discord support means that, if I have any issues with the module I’m configuring, someone is there to help.

Logging is also much more simple as it’s managed by the server. The logs are available for all modules running, and any errors are easy to track down. There are also improvements on the Viam roadmap here to make it even easier to use in future.

## Pricing
OK, this all sounds good, but what about cost? Generally software like this comes with a monthly subscription or one-time fee. In Viam’s case the on-machine software is totally free and open source. 
For cloud-based consumption there is a ‘pay for what you use’ model in place. So for example if I want to train a machine-learning model based on 10k images, I could do that with existing tools offline for free. 

But if I want the ease and convenience of doing that with Viam’s online ML training tools, I’d pay for the storage of the images and the resources used to train the model.

I want to be clear, using the model once it’s trained is purely on-machine, and therefore free. It’s the cloud resources that would be chargeable.

Viam also gives you $5 a month, which is generally enough for basic-usage in the example above,, and the pricing tiers are reasonable if you did want to go beyond that.
If you’re looking for more information on this, take a look at the pricing page or reach out to Viam directly.

## Next Steps
To take advantage of all of the benefits of Viam, I’m planning on migrating my existing python modules into Viam modules and making them available on the registry. I’ll also take some time to review existing Viam modules and see what I can integrate before I re-invent the wheel, so to speak.

One challenge I have had is that my modules rely heavily on the publisher subscriber design pattern, using the pypubsub module, to communicate with each other. I’m working with the Viam team on an approach to manage this so I’ll publish another video with the details soon.

If you’re interested in trying out Viam, visit app.viam.com and create a robot or rent one of theirs to test out.

I’d love to hear more about how you use Viam in your projects, so feel free to join the community and share your progress

[Sign up for Viam's App](https://bit.ly/viam-app-dn)

[Viam on Instagram](https://www.instagram.com/viamrobotics)

[Dan Makes Things on Instagram](https://www.instagram.com/dan.makes.things)

[Join the community](https://bit.ly/makerforge-community)