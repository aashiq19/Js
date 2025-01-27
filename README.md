## JavaScript Promises

### Creating a new Promise!
```
const readFilePro = file => {
  return new Promise((resolve, reject) => {
    fs.readFile(file, (err, data) => {
      if (err) reject('I could not find that file 😢'); // this will be avaible in the catch block
      resolve(data); // data will be sent to the the then block
    });
  });
};

const writeFilePro = (file, data) => {
    return new Promise((resolve, reject) => {
        fs.writeFile(file, data, (err) => {
            if (err) reject("Could not write!!!");
            resolve("Succeed");
        });
    });
};

// INVOKING

readFilePro(`${__dirname}/dog.txt`)
  .then(data => {
    console.log(`Breed: ${data}`);
  }).catch(err => {
    console.log(err);
  });
```

### Consuming a Promise! (ES8)
```
const getDogPic = async () => {
    try {
        const data = await readFilePro(`${__dirname}/dogg.txt`);
        console.log(`Breed: ${data}`);

        const res = await superagent.get(`https://dog.ceo/api/breed/${data}/images/random`);
        console.log(res.body.message);

        await writeFilePro("dog-img.txt", res.body.message);
        console.log("Random dog image saved to file!");
    } catch (err) {
        console.log(err);
    }
};

getDogPic();

// BETTER APPROACH!!!

const getDogPic = async () => {
    try {
        const data = await readFilePro(`${__dirname}/dog.txt`);
        console.log(`Breed: ${data}`);

        const res1Pro = superagent.get(`https://dog.ceo/api/breed/${data}/images/random`);
        const res2Pro = superagent.get(`https://dog.ceo/api/breed/${data}/images/random`);
        const res3Pro = superagent.get(`https://dog.ceo/api/breed/${data}/images/random`);
        const all = await Promise.all([res1Pro, res2Pro, res3Pro]);
        const imgs = all.map((el) => el.body.message);
        console.log(imgs);

        await writeFilePro("dog-img.txt", imgs.join("\n"));
        console.log("Random dog image saved to file!");

    } catch (err) {
        
        console.log(err);
        throw err;
    }
    return "2: READY 🐶";
};

//INVOKING 1

console.log("1: Will get dog pics!");
getDogPic()
    .then((x) => {
        console.log(x);
        console.log("3: Done getting dog pics!");
    })
    .catch((err) => {
        console.log("ERROR 💥");
    });

//INVOKING 2 (better)

(async () => {
    try {
        console.log("1: Will get dog pics!");
        const x = await getDogPic();
        console.log(x);
        console.log("3: Done getting dog pics!");
    } catch (err) {
        console.log("ERROR 💥");
    }
})();
```
