# Youbike Station Search

For the final Vue.js Class assignment, we were tasked to use the open source dataset of Taipei's Youbike Station Dataset,
[YouBike2.0臺北市公共自行車即時資訊](https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json),
to create an interface that allows users to search and find information related to available Youbikes in Taipei. 

The theme design and color scheme is designed with the existing Youbike logo as it's core essence. 

# Feature Overview

- Search bar to allow users to search by station name, area, or address.
- User can execute a search using the `Search` button or by clicking `Enter` after entering the desired text in the input. 
- If no texts are entered and a search is executed, it will show a list of **All Stations**.
- After a search, the user can also click `Clear`, which will reset all search terms and show all stations. 
- The listed cards will only show the `name` of the Youbike Station initially. Users can click the card to expand and see more details. 
- A heart on the card will indicate whether a station has been favorited. Users need to expand and click the **Like** button to add a station to the favorite list.
- Favorited stations will always be shown **at the front** of the list. 
- Users can also choose to remove stations from the list by clicking on the **Remove** button. 
- In the top right corner, there is an extra feature menu **...**, click to show options.
- If `Show Dislike` is checked, then `Removed` stations will be sorted to the bottom of the list. 
- If `Show Dislike` is unchecked, then `Removed` stations will not be visible at all.
- If Users would like to **Reset their favorite list**, the can click on the  `Clear All Favorites` button in the extra menu. This will clear the favorite list. 
- The `Favorite` and `Block` list is saved in the localStorage, therefore the page can be refreshed and the station settings will be retained.

- Below the search bar, there are quick action tags. 
    - `Favorites` - Shows a list of favorite stations only. 
    - `Block List` - Shows a list of stations which have been removed from the full list. (If `Show Dislike` is unchecked, this list will be empty)
    - `Area Specific` - Allow users to quickly search by district areas to speed up the station search