# Buttons

On 26 May 2021, Discord added a new interaction system called buttons. Instead of reactions, bots could not send buttons and users could use this system to interact with bots. This opened up a whole new world of possibilities for bots. Soon after, developers made calculators, polls, games like blackjack, uno and even minecraft!
Buttons provided a clear, easy to use interface for interacting with bots.

So, let's see how we can add buttons to our bot!

## Concept

Buttons weren't the only update to this user interface system in Discord. Discord also added Dropdowns, and even more new features are coming! 

These UI elements reside in a "view". A view can have 5 rows, each row having 5 slots. Each button takes up one slot, and a dropdown takes up all 5 slots of a row. This means that a maximum of 25 buttons can be added to a view. Each message can have just one view.

## Usage Syntax

Let's see how we can create a simple responsive button.

```python
import discord
from discord import commands

bot = discord.Bot() # Create a bot object


class View(discord.ui.View): # Create a class called View that subclasses discord.ui.View
    @discord.ui.button(label="Click me!", style=discord.ButtonStyle.primary, emoji="😎") # Create a button with the label "😎 Click me!" with color Blurple
    async def button_callback(self, button, interaction):
        await interaction.response.send_message("You clicked the button!") # Send a message when the button is clicked


@bot.slash_command() # Create a slash command
async def button(ctx):
    await ctx.respond("This is a button!", view=View()) # Send a message with or View class that contains the button

bot.run("TOKEN") # Run the bot
```

As you can see, we create a class called `View` that [subclasses](#oop) [`discord.ui.View`](https://docs.pycord.dev/en/master/api.html#discord.ui.Button). 

Then, we add a function `button_callback` to the `View` class with the decorator [`discord.ui.button`](https://docs.pycord.dev/en/master/api.html#discord.ui.button). This decorator adds a button to a component. This function takes two arguments: the button that was clicked, and the interaction. These arguments are passed to the function when the button is clicked by the module. We use the [`interaction.response.send_message`](https://docs.pycord.dev/en/master/api.html#discord.InteractionResponse.send_message) function to send a message to the channel where the interaction was sent.

Finally, we create a global Slash Command called `button` that sends the message, along with the view that contains out button.

This is the basic syntax of creating a button. What you create with it is up to you. Enjoy coding amazing things, Pycord will take care of the rest!

## Button Styles

<!-- Button Styles is explained before the other sections because the user should know about disabled buttons, button colors, and so on to be able to understand timeouts -->

| Name     | Usage                          | Color  |
| ----------- | ----------------------------|------- |
| Primary  | `discord.ButtonStyle.primary` / `discord.ButtonStyle.blurple` | Blurple|
| Secondary| `discord.ButtonStyle.secondary` / `discord.ButtonStyle.grey` / `discord.ButtonStyle.gray`| Grey   |
| Success  | `discord.ButtonStyle.success` / `discord.ButtonStyle.green` | Green  |
| Danger   | `discord.ButtonStyle.danger` / `discord.ButtonStyle.red` | Red    |
| Link     | `discord.ButtonStyle.link` / `discord.ButtonStyle.url` | Grey   |

Check out the [`discord.ButtonStyle`](https://docs.pycord.dev/en/master/api.html#discord.ButtonStyle) class for more information.

<img src="https://discord.com/assets/7bb017ce52cfd6575e21c058feb3883b.png" id="button-styles-img">

You can set a buttons style by specifying the `style` argument in the [`discord.ui.button`](https://docs.pycord.dev/en/master/api.html#discord.ui.button) decorator.

```python
class View(discord.ui.View):
    @discord.ui.button(label="Click me!", style=discord.ButtonStyle.success)
    async def button_callback(self, button, interaction):
        await interaction.response.send_message("You clicked the button!")
```

## Action Rows

We have discussed that Views can have 5 rows. Each row has 5 slots, and each button takes up 1 slot. So, how do we move the buttons to another row?

This can be done by specifying the `row` argument in the [`discord.ui.button`](https://docs.pycord.dev/en/master/api.html#discord.ui.button) decorator.

```python
class MyView(discord.ui.View):
    @discord.ui.button(label="Button 1", row=1, style=discord.ButtonStyle.primary)
    async def first_button_callback(self, button, interaction):
        await interaction.response.send_message("You pressed me!")

    @discord.ui.button(label="Button 2", row=2, style=discord.ButtonStyle.primary)
    async def second_button_callback(self, button, interaction):
        await interaction.response.send_message("You pressed me!")
```

## Disabling Buttons

### Making Pre-Disabled Buttons

```python
class MyView(discord.ui.View):
    @discord.ui.button(label="A button", style=discord.ButtonStyle.primary, disabled=True) # pass `disabled=True` to make the button pre-disabled
    async def button_callback(self, button, interaction):
        ...

@bot.command()
async def button(ctx):
    await ctx.send("Press the button!", view=MyView())
```

### Disabling Buttons on Press

**Disabling a Single Button**

```python
class MyView(discord.ui.View):
    @discord.ui.button(label="A button", style=discord.ButtonStyle.primary)
    async def button_callback(self, button, interaction):
        button.disabled = True # set button.disabled to True to disable the button
        button.label = "No more pressing!" # change the button's label to something else
        await interaction.response.edit_message(view=self) # edit the message's view
```

**Disabling All Buttons**

```python
class MyView(discord.ui.View):
        @discord.ui.button(emoji="😀", label="Button 1", style=discord.ButtonStyle.primary)
        async def button_callback(self, button, interaction):
            for child in self.children: # loop through all the children of the view
                child.disabled = True # set the button to disabled
            await interaction.response.edit_message(view=self)

        @discord.ui.button(label="Button 2", style=discord.ButtonStyle.primary)
        async def second_button_callback(self, button, interaction):
            for child in self.children:
                child.disabled = True
            await interaction.response.edit_message(view=self)
```

## Timeouts

Sometimes, you want to have a button that is disabled after a certain amount of time. This can be used to create a "one-time" button, or to restrict a button to a certain amount of time. This is where timeouts come in.

```python

class MyView(discord.ui.View):
    @discord.ui.button(label="A button", style=discord.ButtonStyle.primary, emoji="😎")
    async def button_callback(self, button, interaction):
        await interaction.response.send_message("Button was pressed", ephemeral=True)

@bot.command()
async def button(ctx):
    await ctx.send("Press the button!", view=MyView(timeout=30))
```

As you can see in the last line, we set a timeout of 30 seconds on the view. If you are reusing the view and the timeout will stay the same, you could also define the timeout in the view itself.

```python
class MyView(discord.ui.View):
    def __init__(self):
        super().__init__(timeout=10)

    @discord.ui.button(style=discord.ButtonStyle.primary)
    async def callback(self, button, interaction):
        await interaction.response.send_message("Button was pressed", ephemeral=True)
```

However, in both of the above examples, the view will simply stop working. The button will not be disabled, but it will not respond any more. If someone tries to use it, it will simply show "Interaction Failed". 

Instead, we can create `on_timeout` function in the view. This function will be called when the timeout is reached.

```python
class MyView(discord.ui.View):
    def __init__(self):
        super().__init__(timeout=10)

    async def on_timeout(self):
        for child in self.children:
            child.disabled = True
        await self.message.edit(content="You took too long! Disabling all the buttons.", view=self)

    @discord.ui.button(style=discord.ButtonStyle.primary)
    async def callback(self, button, interaction):
        await interaction.response.send_message("Button was pressed", ephemeral=True)
```

Here, we loop through all the children of the view (buttons and dropdowns in the view) and disable them. Then, we edit the message to show that the timeout was reached.

## Persistent Views

Sometimes, instead of a button that is disabled after a certain amount of time, you want to have a button that always keeps working.

Normally, when the bot goes offline, all the buttons stop working. You will be able to see the buttons, but nothing will happen when you press them. This is a problem if you are trying to create, say, a self-role system with buttons. This is where persistent views come in.

Persistent views work forever. When the bot goes offline, the buttons will stop working, but once the bot is back online, they start working again. 

In a Persistent View, the timeout must be set to `None` and all the children in the view much have a `custom_id` attribute set.

```python
@bot.event
async def on_ready():
    bot.add_view(MyView()) # Registers a View for persistent listening.

class MyView(discord.ui.View):
    def __init__(self):
        super().__init__(timeout=None) # timeout of the view must be set to None

    @discord.ui.button(label="A button", custom_id="button-1", style=discord.ButtonStyle.primary, emoji="😎") # the button has a custom_id set
    async def button_callback(self, button, interaction):
        await interaction.response.send_message("Button was pressed", ephemeral=True)

@bot.command()
async def button(ctx):
    await ctx.send(f"Press the button! View persistence status: {MyView.is_persistent(MyView())}", view=MyView())
```

## FAQ

### I have a question.

If you have any problems, join [Pycord's Discord Server](https://discord.gg/pycord) and ask your question there.

### How many buttons can I have in a message?

Each message can have a maximum of 25 buttons. Views can have up to 5 rows, and each row has 5 slots. A button takes up one slot, which a dropdown takes up all five slots of the row.

### Can I add more than one view to a message?

No. As a Discord limitation, you can only have one view per message.

### Why are buttons so confusing?

Buttons cannot be simple like commands. This system makes them flexible, object-oriented, and does"t limit your imagination. There are loads of different ways you can use views and buttons.

You could create a view with `view = discord.ui.View()` and create classes for each button instead. These button classes can be added to the view with `view.add_item(MyButton())`. If you are creating a link button that opens a a website on press, you could use the system shown below.

```python
view = discord.ui.View()
view.add_item(discord.ui.Button(label="Go to website", url="https://pycord.dev/", style=discord.ButtonStyle.url))
```

The point being, buttons aren't hard to use if you know Python. We recommend learning [Object Oriented Programming with Python](#oop).

### <h3 id="oop">What is OOP? What is subclassing?</h3>

OOP is a programming paradigm that allows you to create objects that have their own properties and methods. Almost everything in python is an object or a class. `discord.Embed` is a class, `discord.ui.View` is a class. When you use ```view = discord.ui.View()``` to create a view, you are actually creating an object of type `discord.ui.View`.

Subclassing is a Python OOP concept. It means that you can create a class that inherits from another class. In other words, the class that subclasses another class can inherit all the methods and attributes of that class.

We highly recommend you learn about basic Python concepts like classes and inheritance before you start learning Pycord.

**Resources**:

  - [W3Schools's Guide to Python Classes & Objects](https://www.w3schools.com/python/python_classes.asp)
  - [W3Schools's Guide to Python Inheritance](https://www.w3schools.com/python/python_inheritance.asp)

### Do buttons need any special permissions?

No. Neither you, nor any server admins need to change anything. No new permissions are needed to enable Buttons in your servers.

### Should I replace reactions with buttons for my bot?

That is up to you. Buttons do provide a cleaner interface for your bot, and are easier to use.