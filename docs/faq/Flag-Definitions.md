---
title: Flag-Definitions
tag: faq
category: faq
---

### What are claim flags?
Claim flags refer to ~3 different related systems in grief defender that allow you to basically control everything that happens in claims. You can even create your own claim flags that you or other players can toggle through the in-built GUI!  

### Root Claim Flags

Root claim flags are those associated with different actions taken in the game. Like for example right clicking a block triggers the root claim flag of interact-block-secondary. Grief Defender monitors most game events so you don't have to worry about actions not being picked up by the plugin except in rare circumstances. The way to actually see Root claim flags in effect is by using the `/gddebug command`. That command is essential in working with claim flags to understand what the plugin is actually doing to debug claim flags. Here is an example output from gddebug where it recorded a grass block breaking  

https://griefdefender.github.io/debug/?rUBiXaQRF3 
What each part of this output means will be discussed later. The flags.yml config file controls whether root flags are enabled or not. It does not control whether claim flags are enabled in the GUI! I really recommend you do not touch this file unless you really know what you are doing as disabling flags can basically break the plugin.  

### GUI Claim Flags

The claim flags in the inventory/chat GUI are claim flags that are more user friendly and understandable. To see the GUI do `/claimflag` or `/cf` while standing in a claim you own (unless you are admin). You can change which GUI you want to be default in the settings but you can always switch the one you are using by doing/claimgui. You can also use `/claimflagdefinition` if you don't like GUI's. By default there are two different groups of claim flags those being ADMIN claim flags and USER claim flags which control things that are reserved for Admins and things that anyone can change respectively. Each claim flag has a human readable name that describes what it does and they can generally only have two values true or false. On the chat GUI clicking the word "true" or "false" changes it between the two values. What this is actually doing requires going into the config specifically to the presets folder and opening up `minecraft.yml`.  

### Example Claim Flag

animal-spawn:                                            The technical name of the claim flag and what will appear in the Chat GUI
contexts:
gd_claim_default=user                I actually don't know what this really does but its the same for almost all claim flags.
default-groups: {}                           I think this is all the flag groups the default-value will apply to but isn't that important, usually empty
default-value: true                         The default value of this claim flag across all claims
enabled: true                                    Whether or not this claim flag will actually show up. This is the place to disable default claim flags if you so wish
icon:
enchanted: false
id: minecraft:pig_spawn_egg             This is all stuff for how the claim flag will be displayed in the Inventory GUI
title: '&6animal-spawn'
owner-mode: true                                     Not 100% sure but think this is whether or not a claim flag will apply to the owner of the claim
permissions:
flag=entity-spawn, target=#animal            Actually how the claim flag works!!! Will explain in more detail below

### How Claim GUI Claim Flags Work

This is a very simple claim flag, but you can make them as complicated as you wish. The flag is set as entity-spawn which is one of the root flags discussed before. The target is the tag #animals which is a vanilla tag used for controlling events related to all animals. This is basically doing the same thing as listing off each individual animal in here although this theoretically future proofs it as mojang is likely to add more animals in the future to that tag when they add new animals. Mojang is pretty good about adding tags although you may want to create your own so you can use it across multiple claim flags. Grief defender thankfully provides a way to create custom tags to use within the plugin in the tags.yml file! Here is an example of a tag I made called #container which basically lists all containers.  

```
tags:
#connainers':
minecraft:dropper
minecraft:dispenser
minecraft:furnace
minecraft:hopper_minecart
minecraft:chest_minecart
minecraft:brewing_stand
minecraft:barrel
minecraft:trapped_chest
minecraft:hopper
minecraft:blastfurnace
minecraft:smoker
minecraft:chest
minecraft:shulker_box
minecraft:light_gray_shulker_box
minecraft:black_shulker_box
minecraft:brown_shu1ker_box
minecraft:yellow_shulker_box
minecraft:green_shu1ker_box
minecraft:purple_shu1ker_box
minecraft:orange_shulker_box
minecraft:lime_shu1ker_box
minecraft:white_shu1ker_box
minecraft:gray_shulker_box
minecraft:blue_shulker_box
minecraft:magenta_shulker_box
minecraft:cyan_shulker_box
```

```
permissions:
- flag=command-execute, target-gsit:sit, source-minecraft:player
- flag=interact-block-secondary, target=#slabs, source-minecraft:player
- flag=interact-block-secondary, target=#wool. carpets, source-minecraft:player
- flag=interact-block-secondary, target=minecraft:moss_carpet source-minecraft:player
- flag-interact-block- econdary, target-minecraft:pale moss_carpet, source-minecraft:player flag-interact-block-secondary, target=minecraft:bi_dripleaf, source-minecraft:player flag-interact-block-secondary target=#stairs source=minecraft:player
- flag=interact-block-secondary, target=#logs, source-minecraft:player 
- flag=interact-block-secondary, target=#pressure_plates, source-minecraft:player
```

In the claim flag you can see that the root flag command-execute is set with the target of gsit:sit which is lingo for controling whether or not you can do the /sit command in people claims. In this case its not necessary, but often it helps to narrow down your claim flags if you add a source to make sure this flag applies to the right entities as in many cases entities can do the same actions players can. The rest of these lines are basically controlling whether or not a player can right click each of these blocks as the gsit plugin maps sitting on blocks to right clicking them with an empty hand. This claim flag basically fully disables or enables other people sitting in a persons claim depending on what the claim owner sets the flag to. Contexts are going to be explained in the next section, but you can use as many contexts as you want for a GUI claim flag.  

### How to interpret Gddebug Outputs

Explaining gddebug and flag contexts go hand in hand so I will explain them together. We are going to go back to the simple /gddebug output from before https://griefdefender.github.io/debug/?rUBiXaQRF3. Each line in the output is an event that Grief Defender monitored for and found during the time between when you started the gddebug and did /gddebug paste. In this output the two lines at the bottom can be ignored as they appear on almost every gddebug with it recording the actual command itself. Flag is simple it is just the root claim flag that is being triggered. Definition lists any GUI claim flags that involve the action that was done, in this case it is the admin claim flag player-block-break. Trust is not what you would expect, I think it is the highest trust required to do this event. In this case it is the claim group builder as a block was broken. Source and Target go hand in hand. Basically the source of an event and what that event acted on. So in this case the source is the player breaking the grass block and the target is the grass block itself. Targets and sources are the most powerful when it comes to creating claim flags and at least having a target is pretty much always necessary. Location and User/Group are self explanatory with the former being location data for where the event occurred and the latter being the specific User or Group that caused an event to occur like the player name. Contexts is pretty much everything else. Contexts primarily work off minecrafts in-built tag system so there are a lot in this case because grass blocks are a part of a ton of tags. Contexts are super helpful at allowing you to do pretty much anything you want with claim flags big or small. Result is the result of the claim flag check, basically did claim flags stop the even from happening or not. In this case I owned the claim so the result was true for me breaking the grass block.  

### Luck Perms Claim Flags

The final type of claim flag I will be talking about is the Luck perms claim flags. Basically the way claim flags are stored is counter intuitive. You would imagine that there is some database or folder full of files in the plugin storing all the GUI claim flag settings, that is not actually the case. The plugin loads the default value from the minecraft.yml file and when you click to change the value of a claim flag it creates an entry in Luck Perms! That is why there is such a heavy emphasis on Luck perms in this plugin as a ton of its data is stored there. Most claim flags are stored in the griefdefender_definition group in luck perms. Here is an example showing how the block-fertilize claim flag is stored in Luck Perms.  

![alt text](https://cdn.discordapp.com/attachments/1008610132655091733/1339527760128774275/image.png?ex=688547b1&is=6883f631&hm=2b375db2188c9666b67f5f3426009aa758687d556886fd89cd664bbc42c8bb75&)

The actual flag is the permission on the left with the value of the flag being set to true in this instance. The contexts are just the source and target that being a player and bonemeal. The only other context is the claim id for the claim this flag is set in which is how the plugin keeps track of which flags go where.

### Creating Custom Claim Flags Method 1

You may have read the last section and thought, well does that mean you can basically skip the step of using a GUI and create a flag in Luck Perms itself? And yes that is absolutely the case and I would recommend admins go in to edit the actual Luck Perms claim flags for a claim if you are changing the minecraft.yml config a lot or are messing with the custom claim flags in a claim that I am about to talk about. But in general its a pain to do that manually, thankfully Grief Defender comes with its own command just to create these! If you don't run /claimflag directly but instead hit space you can choose from any of the root claim flags. If you hit space again you will then be prompted with a list of all tags, blocks, and entities. This part of the command is where you specify the target. Then you can hit space again and you can choose what value  you want to set this claim flag to that you are creating. You can allow things that are normally blocked in claims or the opposite and /gddebug will be very helpful at figuring out what contexts to use and all that. This is the bare minimum to create a custom claim flag, but you are probably going to want to do more with it. If you hit space again it will prompt you with a list of all contexts you can use with source being the most useful one. If you run this command at any point until now it will default to making this claim flag you created only apply to the claim you are standing in. If you want to make a claim flag global you are going to need to set default= to basic. Basically it is setting which claim type this flag will be default in so you could instead set admin or subdivided. Here is what I had to do to make interacting with graves possible on my server as we have a graves plugin.  

![alt text](https://cdn.discordapp.com/attachments/1008610132655091733/1339533849893535834/image.png?ex=68854d5d&is=6883fbdd&hm=853fede27d81e0c7c060c2d8166adcd0a3d771a433facb281a8b176446b059dd&)

I really really do not recommend this method for creating global claim flags, it is frankly too much of a pain in the ass and is prone to a lot of issues. Also this does not come with a fancy GUI that you can use to change the flags per claim at a moments notice. I really would recommend you use method 2 for all claim flags including when doing something as simple as just turning off a feature permanently in claims. What this method excels at is per claim custom claim flags. You can get really creative with these and they allow you to do some cool ass shit. Here are some examples from my server.

Spots where people can only break and place shulker boxes at my server spawn. These each are 1x1x1 3d claims in a claim group so that all the claim flags apply to all of them including all the luck perms flags.

![alt text](https://cdn.discordapp.com/attachments/1008610132655091733/1339536532738146325/2025-02-13_01.54.31.png?ex=68854fdc&is=6883fe5c&hm=7b4c8fac66d5f5be7b6ac7b5aa52807c40522e4d5a5ae000b93700db7c6de825&)
![alt text](https://cdn.discordapp.com/attachments/1008610132655091733/1339536533144866826/image.png?ex=68854fdc&is=6883fe5c&hm=c5f40d3f382b3f7f8f32cfd38e94f6f578e2bf0a19dde1f5c87cce4c51a62b7c&)
![alt text](https://cdn.discordapp.com/attachments/1008610132655091733/1339536533426016317/image.png?ex=68854fdc&is=6883fe5c&hm=e498ed77cc59b6f55f94a7650059555d1c2300aa599106ab5ebee0313e15599d&)
![alt text](https://cdn.discordapp.com/attachments/1008610132655091733/1339536533757235242/image.png?ex=68854fdd&is=6883fe5d&hm=5c477ed4af49168e5273fdb74a561d71ed75ecd5ef23436045ec6d0b68cc96d1&)

People can only right click end crystals on bedrock in the end claim we have basically allowing people to respawn the dragon without spamming end crystals on the obsidian or destroying the claim at all. 

![alt text](https://cdn.discordapp.com/attachments/1008610132655091733/1339537851733508127/2025-02-13_02.01.20.png?ex=68855117&is=6883ff97&hm=7eeea67a19e5e1cc0d07b7fd9e925097c9e2ae7a5eb32b5f90ec4b81f687fa00&)
![alt text](https://cdn.discordapp.com/attachments/1008610132655091733/1339537852215721994/image.png?ex=68855117&is=6883ff97&hm=ab5a27d4ade9a877a6f9b9fe75850d45dd1033ebf7caeb58b168b903e6027c69&)
![alt text](https://cdn.discordapp.com/attachments/1008610132655091733/1339537852693876767/image.png?ex=68855117&is=6883ff97&hm=0ac40db3e1f96abdb06d1d3f3680498c33948bfc1400914eb459c65fd5be6ce9&)
![alt text](https://cdn.discordapp.com/attachments/1008610132655091733/1339537853079748668/image.png?ex=68855117&is=6883ff97&hm=a1433dc7329398418d96d7ce5a9cc6a815e59ccf904d1883a96643d10d501839&)

### Creating Custom Claim Flags Method 2

I basically went over it before, but the minecraft.yml file in the presets folder is the place to go to create new GUI claim flags. There are two sections in the file the admin one and the user one. Its quite simple, which heading a flag is under will determine which menu it shows up in. The list of admin flags comes first in the file so you will have to scroll down a bit to get to the user flags. The easiest way to create a new claim flag is just to copy and paste an existing one and change the details and is what I pretty much do every time I make a custom one. Refer to what I said previously on what each part of each of these flags does, but you should only need to change values between the default-value and the permissions. With setting the permission, it mostly requires referring to what /gddebug says when you do an action and record it. Pro tip with gddebug, finding what you need in the output can be a pain if you have a ton of people on your server so you can do /gddebug record claim to filter the debug to only events that happened in the claim you are standing in. Like I was saying before there are a lot of instances where you are going to find something you want disabled permanently. Like for example, I found an issue in the plugin ages ago where you can change what allays are holding in other peoples claims. I fixed this with an admin claim flag that has a default value of false. Basically nobody will see the claim flag or be able to modify it except for admins so it basically acts as permanent off switch for that. You could expand this even more and turn owner mode to false basically turning off a feature like in this case interacting with allays in all claims! This goes one step further as you can actually change the claim flags of wilderness (places that are unclaimed) so you could turn this claim flag to false in wilderness and turn off this feature server wide! That is how powerful claim flags are, they rise to the level of actual custom gamerules.

That is the full claim flag tutorial hope that if you are reading this you learned something new. I have been using this plugin pretty actively for 1 and half years so I guess you could call me an expert but I don't feel like it since this plugin is the most complex I have ever worked with ever and I am still learning of features it has and how to use them to this day.  Some of this stuff took me months to learn, some took me over a year so I hope nobody has to go through what I did learning this plugin. 