# Amplify-Blog
Sample Project trying out aws- amplify

## Getting Started
These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See deployment for notes on how to deploy the project on a live system.

### Sign up for an AWS account

### Install the Amplify CLI
First, verify that you have node installed and npm
```
node -v
npm -v
```
Then install and configure the amplify cli
```
$ npm install -g @aws-amplify/cli
$ amplify configure
```
### Create a New App
```
npx create-react-app blog-app --typescript
cd blog-app
amplify init
```

### Add the API Category
```
amplify add api
amplify push
```
Follow the instructions for adding the API category choosing the complex generation (one-to-many relation) and let amplify generate everything you need to start your project
- All the Graphql **Queries**, **Mutations** and **Subscriptions** for this application will be generated for you

## Create the User interface for the blogging app
Now it's time to put our blog working

### Add the amplify and react dependencies
```
yarn add aws-amplify aws-amplify-react @types/react
```
### Configure amplify in the index.tsx file
    import Amplify from '@aws-amplify/core';
    import config from './aws-exports';
    Amplify.configure( config );

### Create a Form to display blogs
At root of your project, create a Form.tsx file with the following code:

    import React from 'react';
    interface State {
        name: string
    }
    interface Props {
        onSubmit: ( formValue: State ) => void;
    }
    export class Form extends React.PureComponent<Props, State> {
        state = {
            name: ""
        };
        handleChange = ( e: any ) => {
            const { name, value } = e.target;
            this.setState( { [ name ]: value } as any );
        };
       render() {
          return (
              <form
                  onSubmit={ async e => {
                      e.preventDefault();
                       this.props.onSubmit( this.state );
                  } }
               >
                  <h3>Create Blog</h3>
                  <input
                      name="name"
                      placeholder="Blog Name"
                      value={ this.state.name }
                      onChange={ this.handleChange }
                  />
                  <button type="submit">Save</button>
              </form>
          );
      }
    }

### Add Connect to the app
Add the following to the App.tsx file

    import { Connect } from 'aws-amplify-react';
    import { graphqlOperation } from "aws-amplify";
    import { createBlog } from './graphql/mutations'

    import { Form } from './Form';

There will be compiling errors showing the aws-amplify-react is missing... Bah bah bah...
- Create a new Folder at the root and name it *@types*
- Create a file *aws-amplify-react.d.ts* in that folder

In the *aws-amplify-react.d.ts* file, add the following to silence up errors:

    declare module 'aws-amplify-react';
Now we are good to go. Change the App.tsx file with the following code

    class App extends Component {
      render() {
        return (      
          <div className="App">
            <Connect mutation={graphqlOperation(createBlog)}>
              {({ mutation }: any ) => (
                <Form
                  onSubmit={ async input => {
                    const response = await mutation({ input });
                    console.log( response );
                  }}
              />)}
            </Connect>
          </div>
        );
      }
    }
    export default App;

You can run the app with npm start and see in the browser. Check the console output

### Create a Blog Form to display blogs
Create the Blog.tsx file with the following content:

    import * as React from 'react'
    import { Connect } from "aws-amplify-react";
    import { graphqlOperation } from "aws-amplify";
    import { listBlogs } from './graphql/queries';

    export class Blogs extends React.PureComponent {
      render() {
        return (
            <Connect query={graphqlOperation( listBlogs )}>
                {({ data: {listBlogs: blogs}}: any ) => {
                    if ( !blogs ) {
                        return null;
                    }
                    return blogs.items.map( (b: any) => (
                        <div key={b.id}>{ b.name }</div>
                    ))
                }}
            </Connect>
        )
      }
    }

Now it's time to add the Blog page to our App.tsx

    import { Blogs } from "./Blogs";
    ...
     <Connect mutation={graphqlOperation(createBlog)}>
     ...
     </Connect>
     <Blogs />


## Conclusion
This a nice try of amplify!
Work is still in progress!

## Authors

* **Bernard Adanlessosi** - *Initial work* - [Adanlessossi](https://github.com/adanlessossi)


## Acknowledgments


