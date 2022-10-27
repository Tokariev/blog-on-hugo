---
title: "React Custom Hook"
date: 2022-10-23T20:07:49+02:00
tags:
    - react
keywords:
    - custom-hook
---

# Custom react hook to call an API
```js
import { useState, useEffect } from 'react';
import axios from 'axios';

const API_KEY = process.env.REACT_APP_API_KEY;
const url = `https://api.giphy.com/v1/gifs/random?api_key=${API_KEY}`;

const useGif = (tag) => {
    const [gif, setGif] = useState('');

    const fetchGif = async (tag) => {
        const { data } = await axios.get(tag ? `${url}&tag=${tag}` : url);

        const imageSrc = data.data.images.downsized_large.url;

        setGif(imageSrc);
    }

    useEffect(() => {
        fetchGif(tag);
    }, [tag]);

    return { gif, fetchGif }
}

export default useGif;
```


# Usage
## Call custom react hook
```js

import React from 'react';

import useGif from '../useGif';

const Tag = () => {
    const {gif, fetchGif} = useGif();

    return (
        <div className="container">
            <h1>Random Gif</h1>
            <img width="500" src={gif} alt="Random Gif" />
            <button onClick={fetchGif}>CLICK FOR NEW</button>
        </div>
    );
}

export default Tag;
```

## Call custom react hook with param
```js

import React, { useState } from 'react';

import useGif from '../useGif';

const Tag = () => {
    const [tag, setTag] = useState('dogs');
    const {gif, fetchGif} = useGif(tag);

    return (
        <div className="container">
            <h1>Random {tag} Gif</h1>
            <img width="500" src={gif} alt="Random Gif" />
            <input value={tag} onChange={(e) => setTag(e.target.value)} />
            <button onClick={() => fetchGif(tag)}>CLICK FOR NEW</button>
        </div>
    );
}

export default Tag;
```

{{< youtube O6FhJvcvVOE >}}

