#imported bot from scott_logic, then imported aio and json from browser
from scott_logic import register_bot
from browser import aio
import json
#created an asynchronous function called cocktail_recipe_bot
async def cocktail_recipe_bot(text):
#a response variable which contains an api that searches cocktails by their name
    response = await aio.get(f"https://www.thecocktaildb.com/api/json/v1/1/search.php", 
        data={
        "s": text,
        "limit": 1
    })
    try: #try statement outputs the drinks in chatbot
        #converted json string into python dictionary
        response_dict = json.loads(response.data)
        #variables that contains the drinks name, recipe, image, and whether or not its alcoholic
        recipe = response_dict["drinks"][0]["strInstructions"]
        recipe_name = response_dict["drinks"][0]["strDrink"]
        recipe_image = response_dict["drinks"][0]["strDrinkThumb"]
        recipe_alcholic = response_dict["drinks"][0]["strAlcoholic"]
        #variables
        i=1
        condition = False
        ingredients=[]
        #the while loop checks to see if there is another ingredient in the list by seeing if the next ingredient is a string, is it is not a string it is NoneType and that means that we have all the required ingredients   
        while condition == False:
            num = str(i) #varible turns the numbers into a string 
            if type(response_dict["drinks"][0]["strMeasure" + num]) == str: # this variable is used to convert the current ingredient count into a string 
                measurement = response_dict["drinks"][0]["strMeasure" + num]
            else:# if there is no measurement for the ingredient then just make the measurement blank
                measurement = " "
            ingredient = str(response_dict["drinks"][0]["strIngredient" + num]) # a variable that turns the ingredients into strings 
            ingredients.append(measurement + " " +  ingredient) # adds measurement and ingredient to list
            i= i+1 # i is the count that is used to see which the number of the current ingredient
            condition = type(response_dict["drinks"][0]["strIngredient" + str(i)]) != str
            
        #message variable that creates an output of drink name, recipe, image, and whether or not it has alcoholic 
        message = f"""
        <img src="{recipe_image}" width=100%"/>
        <h3>{recipe_name}, {recipe_alcholic}</h3>
        </br>
        <h5>Ingredients:</h5>
        <ul>
        """ #<ul> creates a list
         # for each ingredient in the ingredients list, add the ingredient to the message 
        for x in ingredients:
            message += f"""
            <li>{x}</li>
            """#adds to the message
        message += f"""
        </br>
        </ul>
        <h5>Recipe:</h5>
        {recipe}
        """
        return message #outputs message
    except Exception as e: # if the try fails, then "drink not found" is returned
        return f"""
            Drink not found.
        """
register_bot(cocktail_recipe_bot, "Cocktail Recipe Bot")#registers bot
