# Instagram_Follower_Checker

This script helps you fetch Instagram user's followers and following data, and find out who is not following you back or who you are not following back.
## Description

This code fetches Instagram user's friendship statistics, displaying:

- **Followers**: Usernames that follow you.
- **Following**: Usernames you follow.
- **PeopleIDontFollowBack**: Usernames that follow you but you don't follow back.
- **PeopleNotFollowingMeBack**: Usernames you follow but don't follow you back.
 
## Website

1. Generate JavaScript code to check Instagram account details. [https://henrylok0.github.io/Instagram_Follower_Checker/index.html](https://henrylok0.github.io/Instagram_Follower_Checker/web/index.html)
   
2. Transform your raw API data into a structured JSON format easily. [https://henrylok0.github.io/Instagram_Follower_Checker/Data_Formatter.html](https://henrylok0.github.io/Instagram_Follower_Checker/web/Data_Formatter.html)

3. Comparator (2-5 Instagram accounts) with the same followers and following Instagram accounts. [https://henrylok0.github.io/Instagram_Follower_Checker/JSON_Comparator.html](https://henrylok0.github.io/Instagram_Follower_Checker/web/JSON_Comparator.html)

## Safety & Privacy

- **Client-Side Execution**: This script runs entirely within your web browser. No data is sent to any third-party servers.
- **Open Source**: The code is fully transparent. You can review the JavaScript code before running it to ensure it only interacts with Instagram's official API.
- **No Credentials Stored**: The script uses your active browser session to fetch data. It does not ask for, store, or transmit your password.

## Usage

1. Make sure you are on the Instagram website when running this script. If you are not on the Instagram website, the script will automatically redirect you to Instagram.

2. Make sure you log in to your account on Instagram and you must follow that account if the account is private.

3. Open your browser's developer tools (usually by pressing `F12` or `Ctrl+Shift+I`) and switch to the "Console" tab.

4. Copy and paste the following code into the console:

```js
if (window.location.origin !== "https://www.instagram.com") {
  window.alert(
    "Hey! You need to be on the instagram site before you run the code. I'm taking you there now but you're going to have to run the code into the console again.",
  );
  window.location.href = "https://www.instagram.com";
  console.clear();
}

const fetchOptions = {
  credentials: "include",
  headers: {
    "X-IG-App-ID": "936619743392459",
  },
  method: "GET",
};

let username;

const sleep = (ms) => new Promise((r) => setTimeout(r, ms));
const random = (min, max) => Math.floor(Math.random() * (max - min)) + min;

// This function handles all of the pagination logic
// Calls the API recursively until there are no more pages to load
const concatFriendshipsApiResponse = async (
  list,
  user_id,
  count,
  next_max_id = "",
) => {
  let url = `https://www.instagram.com/api/v1/friendships/${user_id}/${list}/?count=${count}`;
  if (next_max_id) {
    url += `&max_id=${next_max_id}`;
  }

  const data = await fetch(url, fetchOptions).then((r) => r.json());

  if (data.next_max_id) {
    const timeToSleep = random(800, 1500);
    console.log(
      `Loaded ${data.users.length} ${list}. Sleeping ${timeToSleep}ms to avoid rate limiting`,
    );

    await sleep(timeToSleep);

    return data.users.concat(
      await concatFriendshipsApiResponse(
        list,
        user_id,
        count,
        data.next_max_id,
      ),
    );
  }

  return data.users;
};

// helper methods to make the code a bit more readable
const getFollowers = (user_id, count = 50, next_max_id = "") => {
  return concatFriendshipsApiResponse("followers", user_id, count, next_max_id);
};

const getFollowing = (user_id, count = 50, next_max_id = "") => {
  return concatFriendshipsApiResponse("following", user_id, count, next_max_id);
};

const getUserId = async (username) => {
  let user = username;

  const lower = user.toLowerCase();
  const url = `https://www.instagram.com/api/v1/web/search/topsearch/?context=blended&query=${lower}&include_reel=false`;
  const data = await fetch(url, fetchOptions).then((r) => r.json());

  const result = data.users?.find(
    (result) => result.user.username.toLowerCase() === lower,
  );

  return result?.user?.pk || null;
};

const getUserFriendshipStats = async (username) => {
  const user_id = await getUserId(username);

  if (!user_id) {
    throw new Error(`Could not find user with username ${username}`);
  }

  const followers = await getFollowers(user_id);
  const following = await getFollowing(user_id);

  const followersUsernames = followers.map((follower) =>
    follower.username.toLowerCase(),
  );
  const followingUsernames = following.map((followed) =>
    followed.username.toLowerCase(),
  );

  const followerSet = new Set(followersUsernames);
  const followingSet = new Set(followingUsernames);

  console.log(Array(28).fill("-").join(""));
  console.log(
    `Fetched`,
    followerSet.size,
    "followers and ",
    followingSet.size,
    " following.",
  );

  console.log(
    `If this doesn't seem right then some of the output might be inaccurate`,
  );

  const PeopleIDontFollowBack = Array.from(followerSet).filter(
    (follower) => !followingSet.has(follower),
  );

  const PeopleNotFollowingMeBack = Array.from(followingSet).filter(
    (following) => !followerSet.has(following),
  );

  return {
    Followers: followersUsernames,
    Following: followingUsernames,
    PeopleIDontFollowBack,
    PeopleNotFollowingMeBack,
  };
};

// Make sure you don't delete the quotes
// Replace "example_username" below with your instagram username
//
// Change this:
username = "instagram_username";
//
//
//
getUserFriendshipStats(username).then(console.log);
```

5. Replace the `username` variable in the code with your Instagram username:

```js
username = "instagram_username";
```

6. Press `Enter` to run the code.

7. Wait for the script to complete and check the results in the console. The results will show your followers and following lists, as well as who is not following you back and who you are not following back.

## Notes

- This script is for educational and personal use only. Do not use it for any activities that violate Instagram's terms of service.

## Reference
This GitHub repository references this and creates a GitHub repository that can do all the following: generate JavaScript code, convert API data into JSON format, and compare 2-5 Instagram accounts with the same followers and following Instagram accounts.
https://gist.github.com/abir-taheer/0d3f1313def5eec6b78399c0fb69e4b1


## License

MIT License - see [LICENSE](LICENSE) for details.

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=HenryLok0/Instagram_Follower_Checker&type=Date)](https://star-history.com/#HenryLok0/Instagram_Follower_Checker&Date)
