# Add React in One Minute
https://reactjs.org/docs/add-react-to-a-website.html




## CDN Links
https://reactjs.org/docs/cdn-links.html

<img width="1134" alt="Screenshot 2022-06-01 at 10 28 31 PM" src="https://user-images.githubusercontent.com/17598334/171459678-46749e96-4409-49e9-aa92-05931f790416.png">


### create index.js , index.html


#index.html
          <!DOCTYPE html>
          <html>
            <head>
              <meta charset="UTF-8" />
              <title>Add React in One Minute</title>
            </head>
            <body>

              <h2>Add React in One Minute</h2>
              <p>This page demonstrates using React with no build tooling.</p>
              <p>React is loaded as a script tag.</p>

              <!-- We will put our React component inside this div. -->
              <div id="like_button_container"></div>

              <!-- Load React. -->
              <!-- Note: when deploying, replace "development.js" with "production.min.js". -->
              <script src="https://unpkg.com/react@18/umd/react.development.js" crossorigin></script>
              <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js" crossorigin></script>

              <!-- Load our React component. -->
              <script src="like_button.js"></script>

            </body>
          </html>

# like_button.js
     'use strict';

    const e = React.createElement;

    class LikeButton extends React.Component {
      constructor(props) {
        super(props);
        this.state = { liked: false };
      }

      render() {
        if (this.state.liked) {
          return 'You liked this.';
        }

        return e(
          'button',
          { onClick: () => this.setState({ liked: true }) },
          'Like'
        );
      }
    }

    const domContainer = document.querySelector('#like_button_container');
    const root = ReactDOM.createRoot(domContainer);
    root.render(e(LikeButton));
