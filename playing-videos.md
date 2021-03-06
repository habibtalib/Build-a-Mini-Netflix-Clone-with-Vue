# Playing Videos

Now add the JavaScript logic for playing trailers. The almost-stateless component receives properties \(`props`\) from the parent \(`App`\) component.

## Add the Properties

Add the following code below the `script` tag:

```javascript
export default {
  props: ['cloudinaryInstance', 'movie'],
}
```

`cloudinaryInstance` is an instance of the Cloudinary SDK, which you later initialize in the parent component and pass down as `props` to the `VuePlayer` component. Subsequently, you can, with that instance, create a video player, add the source to the `video` tag in the preceding section, and activate the play-or-pause feature.

The `movie` property, which is the player payload, is a single item from the array of movies in the database. Each item contains a title and a trailer video for rendering.

## Add Video Features

Add a `mounted` lifecycle method to the Vue object with the following code:

```javascript
mounted() {
  this.player = this.cloudinaryInstance.videoPlayer('trailer', {
    transformation: {
        crop: 'crop',
        width: 1200,
        aspect_ratio: '16:9',
        x: 100,
        y: 100,
        gravity: 'center'
      }
  });
  this.player.source(
    this.movie.trailer || 'Marvel_Studios_Black_Panther_-_Official_Trailer_nmrtr7'
  );
  // Mute on blur
  document.hidden && this.player.mute()
  window.onfocus = () => this.player.unmute()
  window.onblur = () => this.player.mute()
},
```

`this.player` is passed the `videoPlayer` result, which then creates a player on the `video` tag. The first parameter passed to `videoPlayer` must match the ID on the `video` tag. The second parameter is an object that holds the details of the video configurations and the transformations you would like to apply to the video.

After creating the player, change its behavior by means of methods. For example:

* Add a `source` method that matches the public ID on your Cloudinary server. The source can come from the `movie` property or default to the Black Panther trailer.
* Because the video plays by default and loops when it ends, you might find that distracting if you are not watching, that is, not on the **Play** tab. As a solution, add code to check if the document is hidden and, if so, mute the player.
* Add two events, `onfocus` and `onblur`, to check if the **Play** tab is active or inactive. Accordingly, unmute or mute, respectively.

## Change the Current Trailer

Next, build a list of movies, from which you can select a movie, after which the trailer player will switch to it and start playing it. To detect the selection of a movie, check the movie property that is passed down and, if its value has been changed by the parent, specify the action that follows.

Add the following code to the `watch` object:

```javascript
watch: {
  movie(newMovie, oldMovie) {
    this.player.source(newMovie.trailer);
  }
},
```

The name of the method \(`movie`\) in that object must match the property of the movie you intend to watch. If the value is different, call the `source` method to update the player.

## Import to `App`

The `VideoPlayer` component functions only if it is mounted in its parent component. To import the component in its script, edit the `App.vue` file to read as follows:

```javascript
import VideoPlayer from './components/VideoPlayer.vue';
export default {
  components: {
    VideoPlayer
  }
};
```

You must specify in `App.vue` that `VidePlayer` is a child by adding it to the list of components.

Next, render the component immediately below the navigation code:

```markup
...
</nav>
<VideoPlayer :cloudinaryInstance="cloudinaryInstance" :movie="movie"></VideoPlayer>
```

In the above code, `cloudinaryInstance` and `movie`, which you will create in the next section, are passed down to the component through the `VideoPlayer` element.

