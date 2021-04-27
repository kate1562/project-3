# Hack-a-snack GA Project 3 
## Overview

Hack-a-Snack is a full-stack application which operates as an online recipe collection. Users can browse a recipe database (seeded from an external API), post their own recipes, comment on others’ posts. The Edamam API was used to seed recipe data and provide the initial content for users to browse. We used Canva to whiteboard initial ideas and to plan the backend MVC, Google Slides was utilised to create a clickthrough wireframe. I was responsible for building the recipes backend and the user profile frontend which made use of React Slick slider library.

🔗 http://hack-a-snack-project.herokuapp.com/

## Brief & Timeframe:
* Build a full-stack application by making your own backend and your own front-end.
* Use an Express API to serve your data from a Mongo database.
* Consume your API with a separate front-end built with React.
* Be a complete product with multiple relationships and CRUD functionality for at least a couple of models.
* Implement thoughtful user stories/wireframes that are significant enough to understand what your MVP will be.
* Have a visually impressive design.
* Be deployed online so it's publicly accessible.
* Have automated tests for at least one RESTful resource on the back-end.
* Work in a team, using git to code collaboratively.
* A link to your hosted working app in the URL section of your GitHub repo.
* A git repository hosted on GitHub, with a link to your hosted project, and frequent commits dating back to the very beginning of the project.
* A working APP hosted on the Internet.
* Timeframe: one week.

### Team 🚀:
* Fabien Depasse : https://github.com/fdepasse
* Emily Randall: https://github.com/emilyrandall1998
* Jess Karia: https://github.com/JessKaria

## Technologies used
### Frontend:
* React JS
* JavaScript (ES6)
* Sass
* Babel
* React-Router
* Webpack
* Axios
* HTML5
* Node.js
* Bulma (CSS framework)
### Backend:
* Express
* Mongoose
* MongoDB
* Webpack
* bcrypt
* jwt
### Testing:
* Chai
* Mocha
### Dev Tools
* GitHub
* Git branching
* VS Code
* Chrome developer tools
* Google Slides
* Canva
* Insomnia
* API : Edamam
## User Journey
### Homepage
![Screenshot 2021-04-06 at 16 39 26](https://user-images.githubusercontent.com/68645584/115594498-b85c8a80-a2cd-11eb-9efe-d8aef8596926.png)

### All Recipes
<img width="1406" alt="Screenshot 2021-04-21 at 18 19 05" src="https://user-images.githubusercontent.com/68645584/115594787-0ffaf600-a2ce-11eb-9b6d-7793281aec26.png">

### Single Recipe
<img width="715" alt="Screenshot 2021-04-21 at 18 20 38" src="https://user-images.githubusercontent.com/68645584/115594976-4769a280-a2ce-11eb-80ee-d70b43f45115.png">

### Post a recipe
<img width="1416" alt="Screenshot 2021-04-21 at 18 22 48" src="https://user-images.githubusercontent.com/68645584/115595221-944d7900-a2ce-11eb-901d-b727cf30f1f6.png">
* access for logged in users only

### User Profile 
<img width="1431" alt="Screenshot 2021-04-21 at 18 21 46" src="https://user-images.githubusercontent.com/68645584/115595099-6ff19c80-a2ce-11eb-9065-0ba418ee962e.png">

### Update Profile modal 
<img width="592" alt="Screenshot 2021-04-21 at 18 21 58" src="https://user-images.githubusercontent.com/68645584/115595119-7718aa80-a2ce-11eb-930c-037c1c96e7b5.png">

### Register & Login
<img width="704" alt="Screenshot 2021-04-21 at 18 23 29" src="https://user-images.githubusercontent.com/68645584/115595310-ad562a00-a2ce-11eb-88c1-b70a456540fd.png">
<img width="1415" alt="Screenshot 2021-04-21 at 18 23 55" src="https://user-images.githubusercontent.com/68645584/115595361-bc3cdc80-a2ce-11eb-8f2b-945224dfcc38.png">


### Functionality
Hack-a-snack works much in the same way as other online recipe directories, users can:
* Register & login
* View a collection of recipes
* View a single recipe and save it to their collection
* View own recipe collection including posted and saved
* View other users' profiles
* Post, edit and delete a recipe
* Post and delete a recipe review

## Process
### Planning
From the outset we came together as a group to discuss desired functionalities and the minimum viable product to deliver. We worked together to produce detailed plans for the backend models, views and controllers before dividing the backend build amongst ourselves.

Google Slides was used to produce a clickthrough wireframe to design the frontend and further solidify understanding of the project as a whole. For example, how the backend and frontend work together. 
<img width="1210" alt="Screenshot 2021-04-21 at 18 25 17" src="https://user-images.githubusercontent.com/68645584/115595517-ed1d1180-a2ce-11eb-99b1-0ead94a900c8.png">
<img width="1190" alt="Screenshot 2021-04-21 at 18 25 36" src="https://user-images.githubusercontent.com/68645584/115595558-f8703d00-a2ce-11eb-8b4f-1b35dc0ca695.png">
<img width="1210" alt="Screenshot 2021-04-21 at 18 25 49" src="https://user-images.githubusercontent.com/68645584/115595589-002fe180-a2cf-11eb-8f66-a00185402968.png">

### Backend 
We built the express App and connected it to a MongoDB database, next the models, views and controllers were set up. We seeded the initial recipe collection from an external public API called Edamam, this tested the models worked as expected. In order to test the backend functionality we used Insomnia to test request responses worked as expected.

I set up the backend router file which handled requests using functions imported from controller files. An example route from the router file:
```javascript
router.route('/recipes/:recipeId')
  .get(recipes.getSingleRecipe)
  .put(secureRoute, recipes.updateRecipe)
  .delete(secureRoute, recipes.deleteRecipe)
```
For this route, the client request should contain a specific recipe id in order to locate that single entry in the database. Secure route middleware (using json web token) was added to certain routes to prevent users from editing or deleting a recipe if they were not logged in.  Depending on the request type (GET, PUT, or DELETE) a different controller function is used for example:
```javascript
async function updateRecipe(req, res, next) {
  const id = req.params.recipeId
  const currentUser = req.currentUser
  const body = req.body

  try {
    const recipeToUpdate = await Recipes.findById(id)
    //check if there's actually a recipe 
    if (!recipeToUpdate) {
      return res.send({ message: 'No recipe found' })
    }
    // check the user is the one who created the recipe || my main man masterchef
    if (!currentUser.isAdmin && !recipeToUpdate.user.equals(currentUser._id)) {
      return res.status(401).send({ message: 'Unauthorized' })
    }
    recipeToUpdate.set(body)
    recipeToUpdate.save()
    res.send(recipeToUpdate)

  } catch (err) {
    console.log(err)
    next()
  }

}
```
The updateRecipe function finds the recipe to update using client request, performs some checks: 
* The recipe id provided exists in the database
* The user is the person that created the recipe or is an admin user
* It then updates the recipe using the json body request, saves the new entry to the database and sends the updated recipe back to the user. 

### Frontend
Once the backend was sufficiently tested we moved on to the front end and set up each page as a React component. We divided the components amongst ourselves to build out functionality and basic design. We checked in to discuss any blockers that arose and solved as a team in order to progress as quickly as possible.

### Featured code
Users can view their own profile via my account or browse others’ using the creator link on the recipe page. Here they can see their personal recipe collection including their own posts and those they’ve saved whilst browsing the site. When clicked each image navigates to the recipe details using react-router-dom. 

The function below identifies the logged-in user and requests their recipes from the backend via axios. Posted and saved recipes are stored as arrays in state, each is then mapped over to return some JSX. React slick slider library was used to produce an image carousel with exact specifications and responsive design applied in settings. 
```javascript
export default function myAccount({ match }) {
  const [savedRecipes, updateSaved] = useState([])
  const [postedRecipes, updatePosted] = useState([])
  const [name, updateName] = useState('')
  const [image, updateImage] = useState('')
  const [loading, updateLoading] = useState(true)
  const token = localStorage.getItem('token')

  useEffect(() => {
    async function getUserRecipes() {
      try {
        const { data } = await axios.get(`/api/user/${getLoggedInUserId()}`, {
          headers: { Authorization: `Bearer ${token}` }
        })
        updatePosted(data.postedRecipes)
        updateSaved(data.savedRecipes)
        updateName(data.username)
        updateImage(data.image)
        updateLoading(false)
      } catch (err) {
        console.log(err)
      }
    }
    getUserRecipes()
  }, [])
```
```javascript
        <Slider {...settingsSaved} style={sliderStyle}>
          {savedRecipes.map(saved => {
            return <Link key={saved._id} to={`/recipes/${saved._id}`}>
              <img className='slideImage' src={saved.image} alt={saved.recipeName} />
              <h5 className="title is-5">{saved.recipeName.length >= 12 ? saved.recipeName.slice(0, 15) + '....' : saved.recipeName}</h5>
            </Link>
          })}
        </Slider>
```
### Known bugs and errors
* When a user views a recipe they’ve previously saved the toggle doesn’t show as “on” 
* When posting a recipe review the user needs to select a rating after they’ve written the text
## Future Features
* Refactor code to include a carousel component that can be easily reused and updated dynamically
* A community social feed to display users’ recently posted recipes

## Key Learnings 
* Using Git branching to share a common codebase and create features independently had a great impact on the efficiency of the build. 
* Reading documentation thoroughly, especially when using a new language or library, was super beneficial. It really helped with my understanding of Mongoose and React including interactions with other languages and debugging. 
* Planning stages really helped to conceptualise the project and gave a clear timeline for delivery. 
