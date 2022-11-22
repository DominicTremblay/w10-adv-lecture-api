# Creating an API with Prisma

## Clone the Express API repo

* `git clone git@github.com:DominicTremblay/w10-adv-lecture-api.git`

* repo: [https://github.com/DominicTremblay/w10-adv-lecture-api](https://github.com/DominicTremblay/w10-adv-lecture-api)

## Prisma setup

1. Install Dependencies

* `npm i -D @types/node @types/express typescript tsc-watch ts-node`
* `npm i prisma @prisma/client`

2. Update `package.json`

2.1 Update scripts

```json
  "scripts": {
    "start": "node dist/src/index.js",
    "dev": "tsc-watch --onSuccess \"node ./dist/server.js\"",
    "seed": "ts-node prisma/seed.ts",
    "build": "tsc"
  },
```

2.2 Remove type module

- `"type": "module"`

3. Initalize Prisma

* `npx prisma init`

4. Enter the db connection string in .env and .env.example

* `DATABASE_URL="postgresql://<username>:<password>@localhost:5432/<database>"`

5. Add tsconfig.json file

```json
{
  "compilerOptions": {
    "sourceMap": true,
    "outDir": "./dist",
    "strict": false,
    "moduleResolution": "node",
    "lib": [
      "ESNext"
    ],
    "esModuleInterop": true
  }
}

```

6. Update db/connection.js with the following:

```js
import {
    PrismaClient
} from '@prisma/client';

const prisma = new PrismaClient();

export default prisma;
```

7. Update file extension to typescript

* change file extension to `.ts`
* update all imports to remove `.js` extension

8. Delete the route files

## Create the DB files

1. Create The Schema

```prisma
model Movie {
  id           Int          @id @default(autoincrement())
  title        String       @unique
  release_date DateTime
  runtime_mins Int
  createdAt    DateTime     @default(now())
  updatedAt    DateTime     @updatedAt
  movie_casts  MovieCast[]
  movie_genres MovieGenre[]
}

model MovieCast {
  id             Int      @id @default(autoincrement())
  character_name String   @unique
  createdAt      DateTime @default(now())
  updatedAt      DateTime @updatedAt
  movie          Movie?   @relation(fields: [movieId], references: [id])
  movieId        Int?
  person         Person?  @relation(fields: [personId], references: [id])
  personId       Int?
}

model Person {
  id          Int         @id @default(autoincrement())
  first_name  String
  last_name   String
  createdAt   DateTime    @default(now())
  updatedAt   DateTime    @updatedAt
  movie_casts MovieCast[]
}

model MovieGenre {
  id        Int      @id @default(autoincrement())
  movie     Movie?   @relation(fields: [movieId], references: [id])
  movieId   Int?
  genre     Genre?   @relation(fields: [genreId], references: [id])
  genreId   Int?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

model Genre {
  id           Int          @id @default(autoincrement())
  genre        String       @unique
  movie_genres MovieGenre[]
  createdAt    DateTime     @default(now())
  updatedAt    DateTime     @updatedAt
}
```

1.1 Create the migrations

- `npx prisma migrate dev --name=init`

2. Create the seed files

2.1 Create the seeds/genres.ts file

```js
export const genres = [{
        genre: 'Action',
    },
    {
        genre: 'Adventure',
    },
    {
        genre: 'Comedy',
    },
    {
        genre: 'Crime',
    },
    {
        genre: 'Documentary',
    },
    {
        genre: 'Drama',
    },
    {
        genre: 'Fantasy',
    },
    {
        genre: 'Horror',
    },
    {
        genre: 'Musical',
    },
    {
        genre: 'Mystery',
    },
    {
        genre: 'Romance',
    },
    {
        genre: 'Science Fiction',
    },
    {
        genre: 'Thriller',
    },
];
```

2.2 Create the seeds/persons.ts file

```js
export const persons = [{
        first_name: 'Amber',
        last_name: 'Midthunter',
    },
    {
        first_name: 'Dakota',
        last_name: 'Beavers',
    },
    {
        first_name: 'Dan',
        last_name: 'DiLiegro',
    },
    {
        first_name: 'Tom',
        last_name: 'Cruise',
    },
    {
        first_name: 'Val',
        last_name: 'Kilmer',
    },
    {
        first_name: 'Miles',
        last_name: 'Teller',
    },
    {
        first_name: 'Jennifer',
        last_name: 'Connelly',
    },
    {
        first_name: 'John',
        last_name: 'Hamm',
    },
    {
        first_name: 'Monica',
        last_name: 'Barbaro',
    },
    {
        first_name: 'Michelle',
        last_name: 'Yeoh',
    },
    {
        first_name: 'Stephanie',
        last_name: 'Hsu',
    },
    {
        first_name: 'Jamie Lee',
        last_name: 'Curtis',
    },
    {
        first_name: 'Tom',
        last_name: 'Hanks',
    },
    {
        first_name: 'Austin',
        last_name: 'Butler',
    },
    {
        first_name: 'Olivia',
        last_name: 'DeJonge',
    },
];
```

2.3 Create the seeds/movies.ts file

```js
export const movies = [{
        title: 'Prey',
        release_date: new Date('2022-08-22'),
        runtime_mins: 99,
        movie_genres: [1, 2],
        movie_casts: [{
                character_name: 'Naru',
                id: 1,
            },
            {
                character_name: 'Taabe',
                id: 2,
            },
            {
                character_name: 'Predator',
                id: 3,
            },
        ],
    },
    {
        title: 'Top Gun: Maverick',
        release_date: new Date('2022-05-22'),
        runtime_mins: 130,
        movie_genres: [1, 5],
        movie_casts: [{
                character_name: "Capt. Pete 'Maverick' Mitchell",
                id: 4,
            },
            {
                character_name: "Adm. Tom 'Iceman' Kazansky",
                id: 5,
            },
            {
                character_name: "Lt. Bradley 'Rooster' Bradshaw",
                id: 6,
            },
            {
                character_name: 'Penny Benjamin',
                id: 7,
            },
            {
                character_name: "Adm. Beau 'Cyclone' Simpson",
                id: 8,
            },
            {
                character_name: "Lt. Natasha 'Phoenix' Trace",
                id: 9,
            },
        ],
    },
    {
        title: 'Everything Everywhere All at Once',
        release_date: new Date('2022-03-25'),
        runtime_mins: 139,
        movie_genres: [1, 2, 3],
        movie_casts: [{
                character_name: 'Evelyn Wang',
                id: 10,
            },
            {
                character_name: 'Joy Wang',
                id: 11,
            },
            {
                character_name: 'Deirdre Beaubeirdre',
                id: 12,
            },
        ],
    },
    {
        title: 'Elvis',
        release_date: new Date('2022-06-24'),
        runtime_mins: 159,
        movie_genres: [6, 9],
        movie_casts: [{
                character_name: 'Colonel Tom Parker',
                id: 13,
            },
            {
                character_name: 'Elvis',
                id: 14,
            },
            {
                character_name: 'Priscilla',
                id: 15,
            },
        ],
    },
];
```

2.4 create `prisma/seed.ts`

```js
import {
    genres
} from './seeds/genres';
import {
    movies
} from './seeds/movies';
import {
    persons
} from './seeds/persons';

import prisma from '../db/connection';

const seedGenres = async (genres) => {
  await prisma.genre.createMany({
    data: genres,
  });
};

const seedPersons = async (persons) => {
  await prisma.person.createMany({
    data: persons,
  });
};

const seedMovies = async (movies) => {
    for (let movie of movies) {
        await prisma.movie.create({
            data: {
                title: movie.title,
                release_date: movie.release_date,
                runtime_mins: movie.runtime_mins,
                movie_genres: {
                    create: movie.movie_genres.map((genreId) => ({
                        genre: {
                            connect: {
                                id: genreId,
                            },
                        },
                    })),
                },
                movie_casts: {
                    create: movie.movie_casts.map((cast) => ({
                        character_name: cast.character_name,
                        person: {
                            connect: {
                                id: cast.id,
                            },
                        },
                    })),
                },
            },
        });
    }
};

const run = async () => {
    await seedGenres(genres);
    await seedPersons(persons);
    await seedMovies(movies);
};

run()
    .catch((err) => {
        console.log(`Error: ${err.message}`);
        process.exit(1);
    })
    .finally(async () => {
        await prisma.$disconnect();
    });
```

## Create the Route Files

### 1. Create the basic CRUD for `routes/moviesRoutes.ts`

```js
import express from 'express';

const router = express.Router();

router.get('/', (req, res) => {
    res.json({
        msg: 'list of movies'
    });
});

router.get('/:id', (req, res) => {
    res.json({
        msg: 'get one movie'
    });
});

router.post('/', (req, res) => {
    res.json({
        msg: 'create movie'
    });
});
router.put('/:id', (req, res) => {
    res.json({
        msg: 'update movie'
    });
});
router.delete('/:id', (req, res) => {
    res.json({
        msg: 'delete a movie'
    });
});

export default router;
```

2. Add moviesRoutes to server.ts

```js
import {
    default as moviesRoutes
} from './routes/moviesRoutes';
app.use('/api/movies', moviesRoutes);
```

3. Create a `db/queries/movieQueries.ts` file

3.1 Create the `getMovieList` query

```js
export const getMovieList = async () => {
            const movieList = await prisma.movie.findMany({
                include: {
                    movie_genres: {
                        include: {
                            genre: true,
                        },
                    },
                    movie_casts: {
                        include: {
                            person: true,
                        },
                    },
                },
            });
```

3.2 Update the route handler

```js
router.get('/', async (req, res) => {
    try {
        const movieList = await getMovieList();
        res.json({
            movieList
        });
    } catch (err) {
        res.json({
            msg: err.message
        });
    }
});
```

3.3 Create `db/queries/getMovieById.ts`

```js
export const getMovieById = async (id) => {
    const movie = await prisma.movie.findUnique({
        where: {
            id,
        },
        include: {
            movie_genres: {
                include: {
                    genre: true,
                },
            },
            movie_casts: {
                include: {
                    person: true,
                },
            },
        },
    });
    return movie;
};
```

3.4 Update the Route Handler

```js
router.get('/:id', async (req, res) => {
    const id = Number(req.params.id);
    try {
        const movie = await getMovieById(id);
        res.json({
            movie
        });
    } catch (err) {
        res.json({
            msg: err.message
        });
    }
});
```

3.5 Create `db/queries/createMovie.ts`

```js
export const createMovie = async ({
    title,
    release_date,
    runtime
}) => {
    const movie = await prisma.movie.create({
        data: {
            title,
            release_date: new Date(release_date),
            runtime_mins: Number(runtime),
        },
    });

    return movie;
};
```

3.6 Update the Route Handler

```js
router.post('/', async (req, res) => {
    try {
        const movie = await createMovie(req.body);
        res.json({
            movie
        });
    } catch (err) {
        res.json({
            msg: err.message
        });
    }
});
```

3.7 Create `db/queries/updateMovie.ts`

```js
export const updateMovie = async (id, movieInfo) => {
    const movie = await prisma.movie.update({
        where: {
            id,
        },
        data: formatMovie(movieInfo),
    });
    return movie;
};
```

3.7.1 Create `helpers/index.ts`

```js
export const formatMovie = (movieInfo) => {
    interface Movie {
        title ? : string;
        runtime_mins ? : number;
        release_date ? : Date;
    }

    const movie: Movie = {};

    if (movieInfo.title) {
        movie.title = movieInfo.title;
    }

    if (movieInfo.runtime_mins) {
        movie.runtime_mins = Number(movieInfo.runtime_mins);
    }

    if (movieInfo.release_date) {
        movie.release_date = new Date(movieInfo.release_date);
    }

    return movie;
};
```

3.8 Update the Route Handler

```js
router.put('/:id', async (req, res) => {
    const id = Number(req.params.id);

    const movie = await updateMovie(id, req.body);
    res.json(movie);
});
```

3.9 Create `db/queries/deleteMovie.ts`

```js
export const deleteMovie = async (id) => {
    const movie = await prisma.movie.delete({
        where: {
            id,
        },
    });

    return movie;
};
```

3.10 Update the Route Handler

```js
router.delete('/:id', async (req, res) => {
    const id = Number(req.params.id);
    try {
        const movie = await deleteMovie(id);
        res.json({
            movie
        });
    } catch (err) {
        res.json({
            msg: err.message
        });
    }
});
```

### 2. Create the basic CRUD for `routes/genreRoutes.ts`

```js
import express from 'express';
import {
    createGenre,
    deleteGenre,
    getGenreById,
    getGenres,
    updateGenre,
} from '../db/queries/genreQueries';

const router = express.Router();

router.get('/', async (req, res) => {
    try {
        const genres = await getGenres();
        res.json(genres);
    } catch (err) {
        res.json({
            msg: err.message
        });
    }
});

router.get('/:id', async (req, res) => {
    const id = Number(req.params.id);
    try {
        const genre = await getGenreById(id);
        res.json(genre);
    } catch (err) {
        res.json({
            msg: err.message
        });
    }
});

router.post('/', async (req, res) => {
    try {
        const genre = await createGenre(req.body);
        res.json(genre);
    } catch (err) {
        res.json({
            msg: err.message
        });
    }
});

router.put('/:id', async (req, res) => {
    const id = Number(req.params.id);

    try {
        const genre = await updateGenre(id, req.body);
        res.json(genre);
    } catch (err) {
        res.json({
            msg: err.message
        });
    }
});

router.delete('/:id', async (req, res) => {
    const id = Number(req.params.id);

    try {
        const genre = await deleteGenre(id);
        res.json(genre);
    } catch (err) {
        res.json({
            msg: err.message
        });
    }
});

export default router;
```

2.1 Create `db/queries/genreQueries.ts`

```js
import prisma from '../connection';

export const getGenres = async () => {
    const genres = await prisma.genre.findMany();

    return genres;
};

export const getGenreById = async (id) => {
    const genre = await prisma.genre.findUnique({
        where: {
            id,
        },
    });

    return genre;
};

export const createGenre = async (genreInfo) => {
    const genre = await prisma.genre.create({
        data: genreInfo,
    });

    return genre;
};

export const updateGenre = async (id, genreInfo) => {
    const genre = await prisma.genre.update({
        where: {
            id,
        },
        data: genreInfo,
    });
    return genre;
};

export const deleteGenre = async (id) => {
    const genre = await prisma.genre.delete({
        where: {
            id,
        },
    });

    return genre;
};
```

2.2 Add the `routes/genreRoutes.ts` to server.ts

```js
import {
    default as genresRoutes
} from './routes/genreRoutes';

app.use('/api/genres', genresRoutes);
```

### 3. Create the basic CRUD for `routes/personRoutes.ts`

```js
import express from 'express';
import {
    createPerson,
    deletePerson,
    getPersonById,
    getPersons,
    updatePerson
} from '../db/queries/personQueries';

const router = express.Router();

router.get('/', async (req, res) => {
    try {
        const persons = await getPersons();
        res.json(persons);
    } catch (err) {
        res.json({
            msg: err.message
        });
    }
});

router.get('/:id', async (req, res) => {
    const id = Number(req.params.id);
    try {
        const person = await getPersonById(id);
        res.json(person);
    } catch (err) {
        res.json({
            msg: err.message
        });
    }
});

router.post('/', async (req, res) => {
    try {
        const person = await createPerson(req.body);
        res.json(person);
    } catch (err) {
        res.json({
            msg: err.message
        });
    }
});

router.put('/:id', async (req, res) => {
    const id = Number(req.params.id);

    try {
        const person = await updatePerson(id, req.body);
        res.json(person);
    } catch (err) {
        res.json({
            msg: err.message
        });
    }
});

router.delete('/:id', async (req, res) => {
    const id = Number(req.params.id);

    try {
        const person = await deletePerson(id);
        res.json(person);
    } catch (err) {
        res.json({
            msg: err.message
        });
    }
});

export default router;
```

3.1 Create `db/queries/personQueries.ts`

```js
import prisma from '../connection';

export const getGenres = async () => {
    const genres = await prisma.genre.findMany();

    return genres;
};

export const getGenreById = async (id) => {
    const genre = await prisma.genre.findUnique({
        where: {
            id,
        },
    });

    return genre;
};

export const createGenre = async (genreInfo) => {
    const genre = await prisma.genre.create({
        data: genreInfo,
    });

    return genre;
};

export const updateGenre = async (id, genreInfo) => {
    const genre = await prisma.genre.update({
        where: {
            id,
        },
        data: genreInfo,
    });
    return genre;
};

export const deleteGenre = async (id) => {
    const genre = await prisma.genre.delete({
        where: {
            id,
        },
    });

    return genre;
};
```

3.2 Add the `routes/personRoutes.ts` to server.ts

```js
import {
    default as personRoutes
} from './routes/personRoutes';

app.use('/api/persons', personRoutes);
```
### 4. Add a Movie Character

4.1 Create the `addMovieCharacter` query in `db/queries/movieQueries.ts`

```js
export const addMovieCharacter = async (movieId, characterName, personId) => {
  console.log(movieId, characterName, personId);

  const movieCast = prisma.movieCast.create({
    data: {
      character_name: characterName,
      person: {
        connect: {
          id: personId,
        },
      },
      movie: {
        connect: {
          id: movieId,
        },
      },
    },
  });

  return movieCast;
};
```

4.2 Create a Nested Route Handler

```js
router.post('/:id/casts', async (req, res) => {
  const movieId = Number(req.params.id);
  const { character_name: characterName } = req.body;
  const personId = Number(req.body.person_id);

  try {
    const movieCharacter = await addMovieCharacter(
      movieId,
      characterName,
      personId
    );

    res.json(movieCharacter);
  } catch (err) {
    console.log(err.message);
    res.json({ msg: err.message });
  }
});
```
