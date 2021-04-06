## Androidology - Part 2 of 3 - Application Lifecycle

> https://youtu.be/fL6gSd4ugSI
>
> Part 2 of 3 in an overview series on the Android platform. In this segment,
> Mike explains the application and process lifecycle as a user navigates
> through different applications.

---

Hi, my name is Mike Cleron. I'm an engineer on the Android Development team.

Android is an open software platform for mobile development. It is intended to
be a complete stack that includes everything from the operating system through
middleware and up through applications.

Now, I'm going to talk about how Android can achieve smooth integration of
components that are written by different authors as user navigates forward and
backward through the experience.

In Android, every application runs in its own process. There's a lot of benefits
to this. It gives you security, protected memory. It means that an application
is doing something CPU intensive, won't block other critical activities, like,
answering a phone. So, all applications are running in their own processes and
the Android system itself is responsible for starting these processes and
shutting them down as necessary to reclaim resources.

> - Applications run in their own processes
> - Processes are started and stopped as needed to run an application's components
> - Processes may be killed to reclaim resources

I have an example that shows how that works.

Now, to explain this example, let me set up the scenario.

In this example, the user is going to navigate from the home screen to their
inbox in the mail application. They're going to select a mail message, from the
mail message, they're going to follow a link out to some Web content in the
browser, and then from the browser, they're going to click on something that
takes them in to the maps application which is a not unreasonable scenario.

Now, when they do this, they're going to actually navigate through four
different applications running in four different processes that are potentially
written by four different authors. From the user point of view, none of that is
important. What they want is to be able to navigate forward, clicking on what
they want to click on, getting the component they want to have responding to
each click, and then they want to be able to go backwards and get back to where
they were. And in particular, they don't want to have to worry about which
applications are running, how much memory they've consumed, how much memory is
left on the device, whether they can launch that next process because all the
resources have been taken up. So the Android system manages all that for them.
Here's how it works.

At the start, the user is on the homepage, there are two processes, in this
example, that are running. There's the system process which contains the
activity manager. That contains the common backstack that is used by all
activities, regardless of which process they run in, and then the home process
is running and that has the home activity running inside it.

Now, before launching the mail application, the system is going to save the
state of the home application just in case, anything bad happens to it, and
you'll see that bad stuff in a few minutes. So the home application has saved
that state in a little parcel that has been moved to the system process. And at
that point, the system can create the mail process, we can then launch the mail
list activity into that process, and now what the user is seeing on the screen,
their mail list. If they click on a particular mail message, then that creates a
request to launch the next activity that goes to the system process. Again,
before a new activity is launched, we save the state of the last activity, so
the mail list saves its state into the system process. It's worth noting that
the state that it saves is not every message in your inbox, it's meta
information about the current state of the activity. Things like, where you are
scrolled to in the list; which item is selected. You don't have to store the
complete mail list in that parcel. So now, that the information has been saved,
then the system is free to launch the next activity that shows a particular mail
message and now that is what the user sees on the screen. From the mail message,
they then click on a link to go out to the Web. So again, another request is
created. The state of the currently running activity is saved in the system
process, and now, we need to create a process to run the browser in. So we
launch the browser process, we launch the browser activity into that process and
the user is now looking at the browser. We're going to do this one time where
the user clicks on a link in the browser to launch a map. We create the request.
We save the state of the foreground activity, in this case the browser, and we
would like to launch the maps application but, as you can see, we're out of
room. So, in order to launch the maps application, we have to find a process
that we can kill. We don't want to kill the home application because we use that
for a navigation hub, we want that to be always available, so it's always
responsive. We don't want to kill the browser because that's the activity the
user just came from. But the mail application is a few frames back on the
backstack. No one is looking at it. It's a perfect candidate to kill, and so
it's gone. Now, what the mail application is gone, we can create the maps
process, and in the maps process, we can create the maps activity and now the
user is looking at the map. So it's worth nothing that all of this happened
without the user being aware of any of the machinery from the user's point of
view. They click on mail, they went to mail. They click on the message, they
went to a message. They click from the message to the Web, they went to the Web.
From the Web, they click on a map, they went to a map. The fact that these are
different processes, different applications, different developers, it's
invisible to the user which is as it should be.

Now, the reason we saved all those little pieces of state information, in the
system process is so that we can go backwards and the user can navigate
backwards as seamlessly as they navigated forward. So, from the current state,
if the user hits back, the first thing that we're going to do is pop the maps
application off the top of the activity stack and restore the browser. The
browser doesn't actually need to restore its state because it's the same object
so, at this point, we can just discard the saved state that was stored in the
system process. And now the user is looking at the browser, that's the
foreground activity. If they hit back again, they are expecting to go back to
the mail message that they came from. Unfortunately, the mail process isn't
running anymore. So the first thing we have to do is make space to run the mail
application. We do that by killing the maps process and starting the mail
process. Once we've done that, we can then launch a fresh copy of the message
activity. This is a new copy that doesn't have any of the state that the user
saw when they left. In order to get it back into the state that the user is
expecting, we then take that saved parcel of information from the system process
and we restore the state of the message activity. Once we've done that, it's
ready to be shown to the user. We can pop the browser off the top of the
activity stack, show them the message activity and they're back where they
started. If we do that one more time, if we hit back again from the mail message
to go back to the mail list, we don't have to create a new process because this
one is still running in the mail process, we just create a new instance of the
mail list activity, that again is a fresh activity to restore its state. We take
the state from the system process. Give that back to the mail list. Now the mail
is the same state that it was when the user left. We can pop the message and the
user back where they came from. So what this example is showing is that the user
can navigate forwards and backwards through multiple activities, they're running
multiple processes, they're provided by multiple developers, and have a very
seamless experience because the Android framework is doing the hard work of
launching and shutting down processes to manage resources and making sure that
the state is preserved as the user expects.

If you're interested in finding out more about Android, I encourage you to visit
the developer site and downloa d the SDK. In the SDK, you'll find a lot more
documentation, and sample code, and you'll also be able to try building
applications of your own. There's also a developer group that you can join to
find out more information, and I also encourage you to check back frequently
because we'll be posting updates to the SDK as the platform matures.

Thank you for watching.