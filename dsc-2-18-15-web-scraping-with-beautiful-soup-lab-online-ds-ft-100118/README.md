
# Web Scraping with Beautiful Soup - Lab

## Introduction

Now that you've read and seen some docmentation regarding the use of Beautiful Soup, its time to practice and put that to work! In this lab you'll formalize some of our example code into functions and scrape the lyrics from an artist of your choice.

## Objectives
You will be able to:
* Scrape Static webpages - Is there a difference between a dynamic and static website?
* Compare APIs vs WebScraping
* Select specific elements from the DOM 

# What we learned
* Beautiful soup is indeed Beautiful - Stephan
    * because of .attrs
* Learned about try and except - Emily
* Inspecting a webpage using 'Inspect' - Me
    

## Link Scraping

Write a function to collect the links to each of the song pages from a given artist page.


```python
#Starter Code
from bs4 import BeautifulSoup
import requests
import xmltodict
import json
from pprint import pprint


# url = 'https://www.azlyrics.com/t/toto.html' #Put the URL of your AZLyrics Artist Page here!
```


```python
html_page = requests.get(url) #Make a get request to retrieve the page
soup = BeautifulSoup(html_page.content, 'html.parser') #Pass the page contents to beautiful soup for parsing


#The example from our lecture/reading
data = [] #Create a storage container
for album_n in range(len(albums)):
    #On the last album, we won't be able to look forward
    if album_n == len(albums)-1:
        cur_album = albums[album_n]
        album_songs = cur_album.findNextSiblings('a')
        for song in album_songs:
            page = song.get('href')
            title = song.text
            album = cur_album.text
            data.append((title, page, album))
    else:
        cur_album = albums[album_n]
        next_album = albums[album_n+1]
        saca = cur_album.findNextSiblings('a') #songs after current album
        sbna = next_album.findPreviousSiblings('a') #songs before next album
        album_songs = [song for song in saca if song in sbna] #album songs are those listed after the current album but before the next one!
        for song in album_songs:
            page = song.get('href')
            title = song.text
            album = cur_album.text
            data.append((title, page, album))
data[:2]
```




    [('Down On Life',
      '../lyrics/elliphant/downonlife.html',
      'EP: "Elliphant" (2012)'),
     ('Tekkno Scene',
      '../lyrics/elliphant/tekknoscene.html',
      'EP: "Elliphant" (2012)')]



## Text Scraping
Write a secondary function that scrapes the lyrics for each song page.


```python
#Remember to open up the webpage in a browser and control-click/right-click and go to inspect!
from bs4 import BeautifulSoup
import requests

#Example page
url = 'https://www.ebay.com/deals?'


html_page = requests.get(url) # requests.get() -> makes a requests then gets the page content of the url
soup = BeautifulSoup(html_page.content, 'html.parser')
soup.prettify()[:1000]
```




    '<!DOCTYPE doctype html>\n<html lang="en">\n <head>\n  <meta charset="utf-8"/>\n  <meta content="IE=Edge" http-equiv="X-UA-Compatible"/>\n  <meta content="width=device-width" name="viewport"/>\n  <meta content="34E98E6F27109BE1A9DCF19658EEEE33" name="msvalidate.01">\n   <meta content="6e11485a66d91eff" name="yandex-verification">\n    <link href="https://ir.ebaystatic.com" rel="preconnect"/>\n    <link href="https://i.ebayimg.com" rel="preconnect"/>\n    <meta content="acf32e2a69cbc2b0" name="y_key">\n     <title>\n      Daily Deals on eBay | Best deals and Free Shipping\n     </title>\n     <meta content="Save money on the best Deals online with eBay Deals. We update our deals daily, so check back for the best deals - Plus Free Shipping" name="description"/>\n     <meta content="8kHr3jd3Z43q1ovwo0KVgo_NZKIEMjthBxti8m8fYTg" name="google-site-verification"/>\n     <link href="https://www.ebay.com/deals" rel="canonical"/>\n     <meta content="unsafe-url" name="referrer"/>\n     <meta content="1026282131252'




```python
cols = soup.find_all("h3", class_="dne-itemtile-title ellipse-2", attrs={"title":True})
```


```python
# go through each h3 and make that a soup object, then call the title on it -> this plan sucks and is unncessary
```


```python
# use the .attrs method on your find_all to get the attributes
all_my_titles = [col.attrs['title'] for col in cols]
all_my_titles
```




    ['Omega XL 60 ct by Great HealthWorks: Small, Potent, Joint Pain Relief - Omega-3',
     "adidas Trefoil Oversize Sweatshirt Women's",
     'Apple iPad Air 2 - WiFi Tablet 16GB 32GB 64GB 128GB 2nd Generation',
     'Crocs Mens Walu Loafer',
     'WG305.1 WORX 8 Amp 14" Electric Chain Saw',
     'Logitech Harmony Smart Control All In One Remote with Hub & Smartphone App Black',
     "Chaps Men's Fleece Flannel 1/4 Zip Jacket",
     'Oakley Thinlink Sunglasses Black Iridium OO9316-03 63mm 9316-03',
     "adidas Cloudfoam Advantage Shoes Women's",
     'DxO One 20.2MP Digital Camera with Wi-Fi - Designed for iOS Devices',
     'Egyptian Comfort 1800 Thread Count 4 Piece Bed Sheet Set Deep Pocket',
     'For Apple iPhone XS Max/XR/XS/X/8/7 Plus 6s Tough Shockproof Armor Hybrid Case',
     'Vornado Whole Room Vortex Space Heater with Remote Control Timer, Gray TVH500',
     '18K Gold Plated Cuban/Curb Link Chain Necklace or Bracelet - Lifetime Warranty',
     'Mario Kart 8 Deluxe for Nintendo Switch - Brand New',
     'Samsonite Pivot 3 Piece Set - Luggage',
     'Sale Price - 1 oz Canadian Gold Maple Leaf $50 Coin (Random Year)',
     'Samsung Galaxy S7 G930V 32GB Verizon - GSM Factory Unlocked Black - Gold',
     'Brand New ZTE Axon M Z999 64GB Carbon Black AT&T GSM GLOBAL Unlocked Dual Screen',
     'Dell S2417DG 24" (23.8" viewable) 16:9 monitor, 2K, 250cd/m2, 165Hz, 1ms, 1,000:',
     'BCP 12V Kids Electric ATV Ride-On Toy w/ 2 Speeds, LED Lights',
     "adidas Alphabounce Basketball Slides Men's",
     'HP 3DV74AA#ABC SSD M700 2.5" SATA III 240GB Planar MLC NAND Internal SSD',
     'Pregnancy Pillow - Full Body Pillow for Maternity & Pregnant Women by PharMeDoc',
     'Samsonite Tenacity 3 Piece Luggage Set - Black, Blue, 25", 21", Backpack',
     '4 TON HYDRAULIC AUTO BODY FRAME TOOLS JACK RAM SHOP SET PORTA POWER REPAIR KIT',
     '2 1 Splash Spill Proof Lid for 20 30oz Ozark Trail RTIC YETI Rambler Tumbler Cup',
     'Bathroom Drain Hair Catcher Bath Stopper Plug Sink Strainer Filter Shower Covers',
     'New 20000/10000Lumens Zoom LED Flashlight Torch Rechargeable 18650 Lamp Light',
     'Auto Window Sun Shade Sock Cover Baby Child UV Protector Shield for Car SUV Van',
     "7'' Car Stereo Radio GPS Navi MP5 Player 2DIN Wifi Bluetooth FM Android 5.1 X2P0",
     '12 Slots Men Watch Box Leather Display Case Organizer Top Glass Jewelry Storage',
     'LG 32" Class HD (720P) LED TV (32LJ500B)',
     '24pcs Rechargeable NiCd AA 2800mAh Ni-Cad Batteries for Solar-Powered Light P24',
     "12x10' Gazebo Canopy Top Replacement 2 Tier Patio Cover for Sunjoy L-GZ288PST-4D",
     'Pair PowerBass S-275CF 2.75" OEM Replacement Speakers For Chrysler+Ford Vehicles',
     'LCD Touch Screen Repair Separator Removal Machine 110V for iphone Samsung HTC',
     'For iPhone X 10 | Ringke [MIRROR] Clear Mirror Shockproof Protective Case Cover',
     'Boss Audio MR6W 6.5” 180 Watt Dual Cone White Weather Resistant Marine Speakers',
     '0~ 20psi Propane Regulator Coupler LP LPG QCC 1 Type Gas Connector 1/4" Thread',
     'Acer Predator XB271HU Abmiprz WQHD 2560 x 1440 (2K) 1ms 144Hz (Overclock Refresh',
     'Carburetor For LCT 208CC 6HP 7HP CMXX MAXX Gas Engine Generator # 20824011 Carb',
     'For LG Aristo 2 Glitter TPU Gradient Phone Case Cover+Black Tempered Glass',
     'Auto Window Sun Shade Sock Cover Baby Child UV Protector Shield for Car SUV Van',
     '6" XGODY 16GB 13MP 4G Unlocked Smartphone Android 6.0 Fingerprint Cell Phone HD',
     'Replacement Silicone Nike Sports Wrist Band Strap Bracelet For Fitbit Surge',
     'OWSOO 8CH 1080N DVR 8pcs 720P 1500TVL Waterproof Camera CCTV Surveillance System',
     'New Tenor Trombone Soft Gig Bag Case Double Aglet Design Black',
     '(2) Technical Pro 8” Studio Monitor Bookshelf Computer Multimedia Speaker Stands',
     'Genuine Apple MJ1M2AM/A USB-C to USB Adapter for Macbook - VG',
     '60L Waterproof Outdoor Backpack Hiking/Climbing/Travelling Bag Day Pack Rucksack',
     '1PC Motorcycle Bike LED Headlight High & Low H4 9003 6000K 40W 4000LM Bulb',
     'Radioddity GD-77S DMR V/UHF Time Slot 2 Voice Prompt Two-way Radio US Commercial',
     'Carburetor Carb For LCT USA CMXX MAXX 414CC Gas Generator Engine 41424001',
     'T-Rex Attack Moose Long Sleeve Ugly Sweater Funny Christmas Shirt (Kelly Green)',
     '10 Drawer Rolling Storage Cart Scrapbook Paper Office School Organizer Clear',
     "PUMA Prowl Alt 2 Wn's Women Shoe Training New",
     'Boston Red Sox 2018 World Series Champs Framed 15" x 17" Collage - Fanatics',
     'Apple Watch - Series 4 - BRAND NEW - 40MM - GPS-WiFi Bluetooth -1 Year Warranty!',
     'Apple Homepod Siri Smart Wi-Fi Speaker - Space Gray',
     'PUMA Ferrari Drift Cat 5 Ultra Sneakers Men Shoe Auto New',
     'Baby Alive Magical Scoops Baby',
     'Samsung Galaxy S9 SM-G960U 64GB Sprint + GSM Unlocked Android Smartphone',
     'Dell Alienware AW3418DW 34" Curved WQHD 4ms 120Hz NVIDIA G-Sync Gaming Monitor',
     'Samsung QN55Q8FN 2018 55" Smart Q LED 4K Ultra HD TV with HDR QLED',
     'Canon EOS 80D 24.2MP Digital SLR Camera',
     'Magic Chef MCM770B 0.7 Cubic-ft, 700-Watt Microwave with Digital Touch',
     'Dyson Ball Animal 2 Upright Vacuum | Purple | Refurbished',
     '80ct Coffee K-Cup Variety Sampler Packs Premium, Bold, Flavored, Party Mix',
     'Vornado VH2 1500 Watt Compact Whole Room Vortex Electric Portable Space Heater',
     'Vortex Razor HD 16-48x65 Angled Spotting Scope',
     'Spypoint Link Evo Cellular Trail Camera Brown Verizon | LINK-EVO-V',
     'Bruin Ambush 410 Crossbow Package w/ Scope, Bolts, Quiver and Cocking Rope',
     'NEW 2018 Camo Barnett Ghost 420 1.5-5X Scope Crossbow Pkg 420 FPS 78501',
     'BCP 12V Kids Remote Control Ride-On Car w/ Lights, MP3, AUX - Black',
     'LOL Surprise Collector Bundle 5-Pack Glitter Pet SideKick Mermaid L.O.L. CHOP',
     'Mega Construx Call of Duty Urban Assault Copter Building Set Bloks FDY78 CHOP',
     'BCP Rocking Horse w/ Sounds - Brown',
     "Columbia Men's Tech Pine Ridge Half Zip Jacket",
     "Bulova Men's 98A140 Automatic Skeleton Window Rose Gold and Silver 45mm Watch",
     "Bulova Precisionist Men's 96B260 Quartz Chronograph 44.5mm Bracelet Watch",
     'Alpine Swiss RFID Blocking ID Card Holder Thin Minimalist Front Pocket Wallet',
     'Ninja Chef 1500 Watt High Speed Blender Mixer Processor Duo w/ Single Serve Cup',
     'KitchenAid Stand Mixer tilt 5-QT rrk150 Refurb Of Ksm150ps Artisan Tilt ALLMetal',
     'Cuisinart 6-Piece Ceramic Cutlery Knife Block Set, Multicolored - C59CE-C6P',
     'Quaker Instant Oatmeal, Cinnamon & Spice Flavor, 10 Packet Boxes, 12 Boxes',
     "Alpine Swiss Pierina Women's Ballet Flats Leather Lined Classic Slip On Shoes",
     "Citizen Eco-Drive Women's EX1484-65D Crystal Accents Two-Tone 29mm Watch",
     "Under Armour Women's UA Tech V-Neck T-Shirt",
     "Bulova Women's 98L203 Crystal Markers and Bezel Quartz Two Tone Dress Watch",
     'Nikon D500 DSLR Camera 20.9MP DX-Format Body +2x xtra Battery + Grip Pro Kit New',
     'LG OLED55B8PUA 55" Smart OLED 4K Ultra HD TV with HDR',
     'Onkyo TX-NR686 7.2 Channel THX Certified Network A/V Receiver Black',
     'LG OLED55E8P 55" 4K HDR Smart AI OLED TV w/ ThinQ - OLED55E8PUA',
     'Versace Bright Crystal Absolu 3.0 EDP Perfume For Women New In Box',
     'Kirkland Minoxidil 5% Extra Strength 12mo Supply Mens Hair Loss Treatment CHOP',
     'Curve by Liz Claiborne 6.7 / 6.8 oz Cologne for Men New In Box',
     "Nature's Best ZERO CARB ISOPURE 3lbs 100% Whey Isolate Protein - PICK FLAVOR",
     'New LG V30 VS996 V30 64GB Silver Verizon Wireless 4G LTE Smartphone',
     'Apple Watch Series 3 42mm GPS Space Gray Aluminum Black Sport Band MQL12LL/A',
     'Samsung Galaxy Note 9 128GB SM-N9600 (FACTORY UNLOCKED) 6.4" Snapdragon 845',
     'Apple iPhone 7 a1778 32GB GSM Unlocked',
     'Car Floor Mats for All Weather Rubber 4pc Set Tactical Fit Heavy Duty Black',
     'Fuelworx Made in the USA Stackable Easy Pour Gas Can CARB Compliant 2.5 Gallon',
     'JEGS 81160 Creeper & Air Seat Set',
     'Powerbuilt 13 Piece  SAE Hex Bit Set with Magnetic Holder, 1/16" to 1/4"',
     'MSI GeForce RTX 2080 GAMING X TRIO Video Card 8GB 256-Bit GDDR6',
     'G.SKILL Ripjaws V Series 16GB (2 x 8GB) 288-Pin DDR4 SDRAM DDR4 3200 (PC4 25600)',
     'AMD RYZEN 5 2400G Quad-Core 3.6 GHz 3.9 Turbo Socket AM4 65W Desktop Processor',
     'G.SKILL TridentZ RGB Series 16GB (2 x 8GB) 288-Pin DDR4 SDRAM DDR4 3000 (PC4 240',
     'Bank Wire Payment. 1 oz Gold Buffalo BU (Random Year) Lot of 10',
     '2018 Mexico 5 oz Silver Libertad BU - SKU#162409',
     "2019 GB 1 oz Gold Queen's Beasts The Falcon (Lot of 10) - SKU #178536",
     '2 x 10 oz .999 Silver Bars - Loaf Poured "Chunky" Bullion #A411',
     '2018 Lenovo ThinkPad X1 Carbon 6th Gen FHD IPS i7-8550U 16GB 512GB PCIe NVMe SSD',
     'Bayonetta 2 - Nintendo Switch',
     '1-2 Switch - Nintendo Switch',
     'SCEPTRE U275W-4000R 27" 4K UHD IPS Monitor HDMI 1.4 & 2.0 Displayport Speakers',
     'New Era 5950 Boston Red Sox 2017 GAME Low Profile Fitted Hat (Navy) MLB Cap',
     'Colin Kaepernick NFL San Francisco 49ers Mid Tier Home Red Jersey Youth (S-XL',
     'NFL Mid Tier Home Away Team Player Official Jersey Collection Youth (S-XL)',
     "Under Armour Women's Power Performance Jersey T-Shirt",
     'BCP 7.5ft Pre-Lit Spruce Hinged Artificial Christmas Tree w/ Stand - Green',
     'Startastic Holiday Halloween & Christmas Outdoor Movie Slide Projector 12 Modes!',
     'USA Bright 12V 5M 16.4ft 3528 5050 5630 RGB SMD 300 LED Flexible Strip light',
     'Outdoor Colorful Snowflake LED Laser Light Projector Landscape Xmas Garden Party',
     'Thayers Rose Petal Witch Hazel Alcohol-Free Toner with Aloe Vera 12 oz',
     'MLB Majestic Player Name & Number Jersey T-Shirt Collection Youth Size (8-20)',
     'Alpine Swiss Men’s Gloves Dressy Genuine Leather Warm Thermal Lined Wrist Strap',
     'Sterling Silver Bermuda Blue Heart Necklace Made with Swarovski Crystal',
     'Samsung Galaxy S8 SM-G950U 64GB GSM Unlocked Android Smartphone (Shadow LCD)',
     'Mini Pedal Stepper Bike Leg Arm Cycling Fitness Exerciser Trainer LCD Display',
     'Apple iPhone X a1901 256GB Smartphone GSM Unlocked',
     'Google Chromecast (2018) (GA00439-US) - Charcoal (6288362) 3rd Generation']




```python
cols[0].span.attrs['itemprop']
```




    'name'




```python
new_urls = [col.a.attrs['href'] for col in cols]
```


```python
new_urls
```




    ['https://www.ebay.com/itm/Omega-XL-60-ct-by-Great-HealthWorks-Small-Potent-Joint-Pain-Relief-Omega-3/292483036552?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/adidas-Trefoil-Oversize-Sweatshirt-Womens/153207790819?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Apple-iPad-Air-2-2nd-WiFi-Cellular-Unlocked-16GB-32GB-64GB-128GB/352252833744?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Crocs-Mens-Walu-Loafer/142861768316?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/WG305-1-WORX-8-Amp-14-Electric-Chain-Saw/252642451443?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Logitech-Harmony-Smart-Control-All-In-One-Remote-with-Hub-Smartphone-App-Black/392028404465?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Chaps-Mens-Fleece-Flannel-1-4-Zip-Jacket/332862495007?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Oakley-Thinlink-Sunglasses-Black-Iridium-OO9316-03-63mm-9316-03/332833573674?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/adidas-Cloudfoam-Advantage-Shoes-Womens/153157702764?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/DxO-One-20-2MP-Digital-Camera-with-Wi-Fi-Designed-for-iOS-Devices/153237842989?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Egyptian-Comfort-1800-Thread-Count-4-Piece-Bed-Sheet-Set-Deep-Pocket/181234756175?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/For-Apple-iPhone-XS-Max-XR-XS-X-8-7-Plus-6s-Tough-Shockproof-Armor-Hybrid-Case/400918113149?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Vornado-Whole-Room-Vortex-Space-Heater-with-Remote-Control-Timer-Gray-TVH500/352086061900?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/18K-Gold-Plated-Cuban-Curb-Link-Chain-Necklace-or-Bracelet-Lifetime-Warranty/351083915039?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Mario-Kart-8-Deluxe-for-Nintendo-Switch-Brand-New/172794122526?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Samsonite-Pivot-3-Piece-Set-Luggage/123465865438?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/1-oz-Canadian-Gold-Maple-Leaf-50-Coin-Random-Year/152782842484?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Samsung-Galaxy-S7-G930V-32GB-Verizon-GSM-Factory-Unlocked-Black-Gold/113298321197?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Brand-New-ZTE-Axon-M-Z999-64GB-Carbon-Black-AT-T-GSM-GLOBAL-Unlocked-Dual-Screen/183446762040?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Dell-S2417DG-24-23-8-viewable-16-9-monitor-2K-250cd-m2-165Hz-1ms-1-000/381880122712?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/BCP-12V-Kids-Electric-ATV-Ride-On-Toy-w-2-Speeds-LED-Lights/361487281839?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/adidas-Alphabounce-Basketball-Slides-Mens/153211500734?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/HP-3DV74AA-ABC-SSD-M700-2-5-SATA-III-240GB-Planar-MLC-NAND-Internal-SSD/183514852885?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Pregnancy-Pillow-Full-Body-Pillow-for-Maternity-Pregnant-Women-by-PharMeDoc/192480839848?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Samsonite-Tenacity-3-Piece-Luggage-Set-Black-Blue-25-21-Backpack/122759903267?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/4-TON-HYDRAULIC-AUTO-BODY-FRAME-TOOLS-JACK-RAM-SHOP-SET-PORTA-POWER-REPAIR-KIT/191864609597?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/2-1x-Splash-Spill-Proof-Lid-for-20-30oz-Ozark-Trail-RTIC-YETI-Rambler-TumblerCup/311794396359?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Bathroom-Drain-Hair-Catcher-Bath-Stopper-Plug-Sink-Strainer-Filter-Shower-Covers/352205981059?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/New-20000-10000Lumens-Zoom-LED-Flashlight-Torch-Rechargeable-18650-Lamp-Light/202454543559?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Auto-Window-Sun-Shade-Sock-Cover-Baby-Child-UV-Protector-Shield-for-Car-SUV-Van/391418369515?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/7-Car-Stereo-Radio-GPS-Navi-MP5-Player-2DIN-Wifi-Bluetooth-USB-FM-Android-5-1/172994158752?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/12-Slots-Men-Watch-Box-Leather-Display-Case-Organizer-Top-Glass-Jewelry-Storage/232663989012?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/LG-32-Class-HD-720P-LED-TV-32LJ500B/112868872523?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/24pcs-Rechargeable-NiCd-AA-2800mAh-Ni-Cad-Batteries-for-Solar-Powered-Light-P24/112976756746?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/12x10-Gazebo-Canopy-Top-Replacement-2-Tier-Patio-Cover-for-Sunjoy-L-GZ288PST-4D/382015836720?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Pair-PowerBass-S-275CF-2-75-OEM-Replacement-Speakers-For-Chrysler-Ford-Vehicles/302102978161?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/New-LCD-Touch-Screen-Repair-Separator-Removal-Machine-for-iphone-Samsung-HTC/382202274047?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/For-iPhone-X-10-Ringke-MIRROR-Clear-Mirror-Shockproof-Protective-Case-Cover/263261660945?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Boss-Audio-MR6W-6-5-180-Watt-Dual-Cone-White-Weather-Resistant-Marine-Speakers/302032899940?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/0-20psi-Propane-Regulator-Coupler-LP-LPG-QCC-1-Type-Gas-Connector-1-4-Thread/332144993594?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Acer-Predator-XB271HU-Abmiprz-WQHD-2560-x-1440-2K-1ms-144Hz-Overclock-Refresh/302249056432?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Carburetor-For-LCT-208CC-6HP-7HP-CMXX-MAXX-Gas-Engine-Generator-20824011-Carb/272706048446?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/For-LG-Aristo-2-Glitter-TPU-Gradient-Phone-Case-Cover-Black-Tempered-Glass/192449496710?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Auto-Window-Sun-Shade-Sock-Cover-Baby-Child-UV-Protector-Shield-for-Car-SUV-Van/391418369375?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/6-XGODY-16GB-13MP-4G-Unlocked-Smartphone-Android-6-0-Fingerprint-Cell-Phone-HD/112535332909?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Replacement-Silicone-Nike-Sports-Wrist-Band-Strap-Bracelet-For-Fitbit-Surge/142736041528?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/OWSOO-8CH-1080N-DVR-8pcs-720P-1500TVL-Waterproof-Camera-CCTV-Surveillance-System/172744536583?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/New-Tenor-Trombone-Soft-Gig-Bag-Case-Double-Aglet-Design-Black/131172183606?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/2-Technical-Pro-8-Studio-Monitor-Bookshelf-Computer-Multimedia-Speaker-Stands/302699624327?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Genuine-Apple-MJ1M2AM-A-USB-C-to-USB-Adapter-for-Macbook-VG/202025279003?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/60L-Waterproof-Outdoor-Backpack-Hiking-Climbing-Travelling-Bag-Day-Pack-Rucksack/112496219403?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/1PC-Motorcycle-Bike-LED-Headlight-High-Low-H4-9003-6000K-49W-4900LM-Bulb/261969580758?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Radioddity-GD-77S-DMR-V-UHF-Time-Slot-2-Voice-Prompt-Two-way-Radio-US-Commercial/142729566587?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/Carburetor-Carb-For-LCT-USA-CMXX-MAXX-414CC-Gas-Generator-Engine-41424001/272678924095?_trkparms=5373%3A0%7C5374%3AFeatured',
     'https://www.ebay.com/itm/T-Rex-Attack-Moose-Long-Sleeve-Crew-Neck-Ugly-Christmas-Sweater-Funny-Shirt/311771701980?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003158',
     'https://www.ebay.com/itm/10-Drawer-Rolling-Storage-Cart-Scrapbook-Paper-Office-School-Organizer-Clear/201529651584?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003158',
     'https://www.ebay.com/itm/PUMA-Prowl-Alt-2-Wns-Women-Shoe-Training-New/283119405668?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003158',
     'https://www.ebay.com/itm/Boston-Red-Sox-2018-World-Series-Champs-Framed-15-x-17-Collage-Fanatics/332857730287?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003158',
     'https://www.ebay.com/itm/Apple-Watch-Series-4-BRAND-NEW-40MM-GPS-WiFi-Bluetooth-1-Year-Warranty/163329295797?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004312',
     'https://www.ebay.com/itm/Apple-Homepod-Siri-Smart-Wi-Fi-Speaker-Space-Gray/401540310865?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004312',
     'https://www.ebay.com/itm/PUMA-Ferrari-Drift-Cat-5-Ultra-Sneakers-Men-Shoe-Auto-New/283142235605?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004312',
     'https://www.ebay.com/itm/Baby-Alive-Magical-Scoops-Baby-Dark-Brown-Hair/222396033061?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004312',
     'https://www.ebay.com/itm/Samsung-Galaxy-S9-SM-G960U-64GB-Sprint-GSM-Unlocked-Android-Smartphone/253866848031?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000001691',
     'https://www.ebay.com/itm/Dell-Alienware-AW3418DW-34-Curved-WQHD-4ms-120Hz-NVIDIA-G-Sync-Gaming-Monitor/382291335699?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000001691',
     'https://www.ebay.com/itm/Samsung-QN55Q8FN-2018-55-Smart-Q-LED-4K-Ultra-HD-TV-with-HDR-QLED/392017180265?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000001691',
     'https://www.ebay.com/itm/Canon-EOS-80D-24-2MP-Digital-SLR-Camera/202221496361?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000001691',
     'https://www.ebay.com/itm/Magic-Chef-MCM770B-0-7-Cubic-ft-700-Watt-Microwave-with-Digital-Touch/292439222629?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003710',
     'https://www.ebay.com/itm/Dyson-Ball-Animal-2-Upright-Vacuum-Purple-Refurbished/272970404242?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003710',
     'https://www.ebay.com/itm/Coffee-K-Cup-Sampler-Packs-Choose-from-Bold-Flavored-Reg-or-Party-Mix-80-Ct/253926307354?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003710',
     'https://www.ebay.com/itm/Vornado-VH2-1500-Watt-Compact-Whole-Room-Vortex-Electric-Portable-Space-Heater/232658255453?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003710',
     'https://www.ebay.com/itm/Vortex-Razor-HD-16-48x65-Angled-Spotting-Scope/282802259982?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000001506',
     'https://www.ebay.com/itm/Spypoint-Link-Evo-Cellular-Trail-Camera-Brown-Verizon-LINK-EVO-V/312202347946?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000001506',
     'https://www.ebay.com/itm/Bruin-Ambush-410-Crossbow-Package-w-Scope-Bolts-Quiver-and-Cocking-Rope/362049153350?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000001506',
     'https://www.ebay.com/itm/NEW-2018-Camo-Barnett-Ghost-420-1-5-5X-Scope-Crossbow-Pkg-420-FPS-78501/202056545000?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000001506',
     'https://www.ebay.com/itm/BCP-12V-Kids-Remote-Control-Ride-On-Car-w-Lights-MP3-AUX-Black/311551011173?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004297',
     'https://www.ebay.com/itm/LOL-Surprise-Collector-Bundle-5-Pack-Glitter-Pet-SideKick-Mermaid-L-O-L-CHOP/264020186298?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004297',
     'https://www.ebay.com/itm/Mega-Construx-Call-of-Duty-Urban-Assault-Copter-Building-Set-Bloks-FDY78-CHOP/263960214549?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004297',
     'https://www.ebay.com/itm/BCP-Rocking-Horse-w-Sounds-Brown/361336678250?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004297',
     'https://www.ebay.com/itm/Columbia-Mens-Tech-Pine-Ridge-Half-Zip-Jacket/332668511616?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004299',
     'https://www.ebay.com/itm/Bulova-Mens-98A140-Automatic-Skeleton-Window-Rose-Gold-and-Silver-45mm-Watch/291885231008?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004299',
     'https://www.ebay.com/itm/Bulova-Precisionist-Mens-96B260-Quartz-Chronograph-44-5mm-Bracelet-Watch/263190996510?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004299',
     'https://www.ebay.com/itm/Alpine-Swiss-RFID-Blocking-ID-Card-Holder-Thin-Minimalist-Front-Pocket-Wallet/362360655165?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004299',
     'https://www.ebay.com/itm/Ninja-Chef-1500-Watt-High-Speed-Blender-Mixer-Processor-Duo-w-Single-Serve-Cup/232851630467?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004296',
     'https://www.ebay.com/itm/KitchenAid-Stand-Mixer-tilt-5-QT-rrk150-Refurb-Of-Ksm150ps-Artisan-Tilt-ALLMetal/361100492861?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004296',
     'https://www.ebay.com/itm/Cuisinart-6-Piece-Ceramic-Cutlery-Knife-Block-Set-Multicolored-C59CE-C6P/312081306485?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004296',
     'https://www.ebay.com/itm/Quaker-Instant-Oatmeal-Cinnamon-Spice-Flavor-10-Packet-Boxes-12-Boxes/382552224064?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004296',
     'https://www.ebay.com/itm/Alpine-Swiss-Pierina-Womens-Ballet-Flats-Leather-Lined-Classic-Slip-On-Shoes/362142649765?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004302',
     'https://www.ebay.com/itm/Citizen-Eco-Drive-Womens-EX1484-65D-Crystal-Accents-Two-Tone-29mm-Watch/263929010259?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004302',
     'https://www.ebay.com/itm/Under-Armour-Womens-UA-Tech-V-Neck-T-Shirt/332722636335?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004302',
     'https://www.ebay.com/itm/Bulova-Womens-98L203-Crystal-Markers-and-Bezel-Quartz-Two-Tone-Dress-Watch/291979717916?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004302',
     'https://www.ebay.com/itm/Nikon-D500-DSLR-Camera-20-9MP-DX-Format-Body-2x-xtra-Battery-Grip-Pro-Kit-New/173319826745?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004217',
     'https://www.ebay.com/itm/LG-OLED55B8PUA-55-Smart-OLED-4K-Ultra-HD-TV-with-HDR/253960494788?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004217',
     'https://www.ebay.com/itm/Onkyo-TX-NR686-7-2-Channel-THX-Certified-Network-A-V-Receiver-Black/222973741553?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004217',
     'https://www.ebay.com/itm/LG-OLED55E8P-55-4K-HDR-Smart-AI-OLED-TV-w-ThinQ-OLED55E8PUA/253507399272?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004217',
     'https://www.ebay.com/itm/Versace-Bright-Crystal-Absolu-3-0-EDP-Perfume-For-Women-New-In-Box/401025243083?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004306',
     'https://www.ebay.com/itm/Kirkland-Minoxidil-5-Extra-Strength-12mo-Supply-Mens-Hair-Loss-Treatment-CHOP/262990311154?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004306',
     'https://www.ebay.com/itm/Curve-by-Liz-Claiborne-6-7-6-8-oz-Cologne-for-Men-New-In-Box/401071955383?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004306',
     'https://www.ebay.com/itm/Natures-Best-ZERO-CARB-ISOPURE-3lbs-100-Whey-Isolate-Protein-PICK-FLAVOR/290926068421?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004306',
     'https://www.ebay.com/itm/New-LG-V30-VS996-V30-64GB-Silver-Verizon-Wireless-4G-LTE-Smartphone/401606614203?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003253',
     'https://www.ebay.com/itm/Apple-Watch-Series-3-42mm-GPS-Space-Gray-Aluminum-Black-Sport-Band-MQL12LL-A/362218335931?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003253',
     'https://www.ebay.com/itm/Samsung-Galaxy-Note-9-128GB-SM-N9600-FACTORY-UNLOCKED-6-4-Snapdragon-845/132742920393?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003253',
     'https://www.ebay.com/itm/Apple-iPhone-7-a1778-32GB-GSM-Unlocked/252816011949?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003253',
     'https://www.ebay.com/itm/Car-Floor-Mats-for-All-Weather-Rubber-4pc-Set-Tactical-Fit-Heavy-Duty-Black/390898357888?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000000126',
     'https://www.ebay.com/itm/Fuelworx-Made-in-the-USA-Stackable-Easy-Pour-Gas-Can-CARB-Compliant-2-5-Gallon/173447172748?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000000126',
     'https://www.ebay.com/itm/JEGS-81160-Creeper-Air-Seat-Set/400902226369?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000000126',
     'https://www.ebay.com/itm/Powerbuilt-13-Piece-SAE-Hex-Bit-Set-with-Magnetic-Holder-1-16-to-1-4/271862533983?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000000126',
     'https://www.ebay.com/itm/MSI-GeForce-RTX-2080-GAMING-X-TRIO-Video-Card-8GB-256-Bit-GDDR6/382579978539?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004300',
     'https://www.ebay.com/itm/G-SKILL-Ripjaws-V-Series-16GB-2-x-8GB-288-Pin-DDR4-SDRAM-DDR4-3200-PC4-25600/301808972784?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004300',
     'https://www.ebay.com/itm/AMD-RYZEN-5-2400G-Quad-Core-3-6-GHz-3-9-Turbo-Socket-AM4-65W-Desktop-Processor/292449297869?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004300',
     'https://www.ebay.com/itm/G-SKILL-TridentZ-RGB-Series-16GB-2-x-8GB-288-Pin-DDR4-SDRAM-DDR4-3000-PC4-240/302221666349?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004300',
     'https://www.ebay.com/itm/SPECIAL-PRICE-BANK-WIRE-PAYMENT-1-oz-Gold-Buffalo-BU-Random-Year-Lot-of-10/142685356389?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000000123',
     'https://www.ebay.com/itm/2018-Mexico-5-oz-Silver-Libertad-BU-SKU-162409/142741065805?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000000123',
     'https://www.ebay.com/itm/SPECIAL-PRICE-BANK-WIRE-2019-GB-1-oz-Gold-Queens-Beasts-The-Falcon-Lot-of-10/113347258206?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000000123',
     'https://www.ebay.com/itm/2-x-10-oz-999-Silver-Bars-Loaf-Poured-Chunky-Bullion-A411/311727080518?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000000123',
     'https://www.ebay.com/itm/2018-Lenovo-ThinkPad-X1-Carbon-6th-Gen-FHD-IPS-i7-8550U-16GB-512GB-PCIe-NVMe-SSD/173546956420?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003490',
     'https://www.ebay.com/itm/Bayonetta-2-Nintendo-Switch/173416378494?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003490',
     'https://www.ebay.com/itm/1-2-Switch-Nintendo-Switch/173445134399?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003490',
     'https://www.ebay.com/itm/SCEPTRE-U275W-4000R-27-4K-UHD-IPS-Monitor-HDMI-1-4-2-0-Displayport-Speakers/292201034872?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003490',
     'https://www.ebay.com/itm/New-Era-5950-Boston-Red-Sox-2017-GAME-Low-Profile-Fitted-Hat-Navy-MLB-Cap/132268189248?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004236',
     'https://www.ebay.com/itm/Colin-Kaepernick-NFL-San-Francisco-49ers-Mid-Tier-Home-Red-Jersey-Youth-S-XL/222915128521?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004236',
     'https://www.ebay.com/itm/NFL-Mid-Tier-Home-Away-Team-Player-Official-Jersey-Collection-Youth-S-XL/392013497544?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004236',
     'https://www.ebay.com/itm/Under-Armour-Womens-Power-Performance-Jersey-T-Shirt/332741320953?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004236',
     'https://www.ebay.com/itm/BCP-7-5ft-Pre-Lit-Spruce-Hinged-Artificial-Christmas-Tree-w-Stand-Green/312217310524?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004007',
     'https://www.ebay.com/itm/Startastic-Holiday-Halloween-Christmas-Outdoor-Movie-Slide-Projector-12-Modes/123287542425?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004007',
     'https://www.ebay.com/itm/USA-Bright-12V-5M-16-4ft-3528-5050-5630-RGB-SMD-300-LED-Flexible-Strip-light/301681651381?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004007',
     'https://www.ebay.com/itm/Outdoor-Colorful-Snowflake-LED-Laser-Light-Projector-Landscape-Xmas-Garden-Party/292735498676?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004007',
     'https://www.ebay.com/itm/Thayers-Rose-Petal-Witch-Hazel-Alcohol-Free-Toner-with-Aloe-Vera-12-oz/232075134401?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000002859',
     'https://www.ebay.com/itm/MLB-Majestic-Player-Name-Number-Jersey-T-Shirt-Collection-Youth-Size-8-20/272785398880?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000002859',
     'https://www.ebay.com/itm/Alpine-Swiss-Men-s-Gloves-Dressy-Genuine-Leather-Warm-Thermal-Lined-Wrist-Strap/311733447856?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000002859',
     'https://www.ebay.com/itm/Sterling-Silver-Bermuda-Blue-Heart-Necklace-Made-with-Swarovski-Crystal/360932995710?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000002859',
     'https://www.ebay.com/itm/Samsung-Galaxy-S8-SM-G950U-64GB-GSM-Unlocked-Android-Smartphone-Shadow-LCD/142914496069?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003712',
     'https://www.ebay.com/itm/Mini-Pedal-Stepper-Bike-Leg-Arm-Cycling-Fitness-Exerciser-Trainer-LCD-Display/142470574663?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003712',
     'https://www.ebay.com/itm/Apple-iPhone-X-a1901-256GB-Smartphone-GSM-Unlocked/152924515886?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003712',
     'https://www.ebay.com/itm/Google-Chromecast-2018-GA00439-US-Charcoal-6288362-3rd-Generation/323496413576?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003712']




```python
def get_url_response(url):
    resp_page = requests.get(url) # requests.get() -> makes a requests then gets the page content of the url
    soup_ = BeautifulSoup(resp_page.content, 'html.parser')
    try:
        rating = soup_.find('a', class_="reviews-star-rating")
        rating_string = rating.attrs["title"].split(" ")[0]
        return rating_string
    except AttributeError as e:
        print("{} \n has no ratings - {}".format(url, e))
        return None
```


```python
ratings = [get_url_response(url=url_) for url_ in new_urls]
```

    https://www.ebay.com/itm/adidas-Trefoil-Oversize-Sweatshirt-Womens/153207790819?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/Apple-iPad-Air-2-2nd-WiFi-Cellular-Unlocked-16GB-32GB-64GB-128GB/352252833744?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/Crocs-Mens-Walu-Loafer/142861768316?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/Chaps-Mens-Fleece-Flannel-1-4-Zip-Jacket/332862495007?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/adidas-Cloudfoam-Advantage-Shoes-Womens/153157702764?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/Egyptian-Comfort-1800-Thread-Count-4-Piece-Bed-Sheet-Set-Deep-Pocket/181234756175?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/For-Apple-iPhone-XS-Max-XR-XS-X-8-7-Plus-6s-Tough-Shockproof-Armor-Hybrid-Case/400918113149?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/18K-Gold-Plated-Cuban-Curb-Link-Chain-Necklace-or-Bracelet-Lifetime-Warranty/351083915039?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/Samsonite-Pivot-3-Piece-Set-Luggage/123465865438?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/1-oz-Canadian-Gold-Maple-Leaf-50-Coin-Random-Year/152782842484?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/Samsung-Galaxy-S7-G930V-32GB-Verizon-GSM-Factory-Unlocked-Black-Gold/113298321197?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/Brand-New-ZTE-Axon-M-Z999-64GB-Carbon-Black-AT-T-GSM-GLOBAL-Unlocked-Dual-Screen/183446762040?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/adidas-Alphabounce-Basketball-Slides-Mens/153211500734?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/Pregnancy-Pillow-Full-Body-Pillow-for-Maternity-Pregnant-Women-by-PharMeDoc/192480839848?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/Samsonite-Tenacity-3-Piece-Luggage-Set-Black-Blue-25-21-Backpack/122759903267?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/2-1x-Splash-Spill-Proof-Lid-for-20-30oz-Ozark-Trail-RTIC-YETI-Rambler-TumblerCup/311794396359?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/Bathroom-Drain-Hair-Catcher-Bath-Stopper-Plug-Sink-Strainer-Filter-Shower-Covers/352205981059?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/New-20000-10000Lumens-Zoom-LED-Flashlight-Torch-Rechargeable-18650-Lamp-Light/202454543559?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/7-Car-Stereo-Radio-GPS-Navi-MP5-Player-2DIN-Wifi-Bluetooth-USB-FM-Android-5-1/172994158752?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/24pcs-Rechargeable-NiCd-AA-2800mAh-Ni-Cad-Batteries-for-Solar-Powered-Light-P24/112976756746?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/Pair-PowerBass-S-275CF-2-75-OEM-Replacement-Speakers-For-Chrysler-Ford-Vehicles/302102978161?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/New-LCD-Touch-Screen-Repair-Separator-Removal-Machine-for-iphone-Samsung-HTC/382202274047?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/For-iPhone-X-10-Ringke-MIRROR-Clear-Mirror-Shockproof-Protective-Case-Cover/263261660945?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/0-20psi-Propane-Regulator-Coupler-LP-LPG-QCC-1-Type-Gas-Connector-1-4-Thread/332144993594?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/For-LG-Aristo-2-Glitter-TPU-Gradient-Phone-Case-Cover-Black-Tempered-Glass/192449496710?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/6-XGODY-16GB-13MP-4G-Unlocked-Smartphone-Android-6-0-Fingerprint-Cell-Phone-HD/112535332909?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/Replacement-Silicone-Nike-Sports-Wrist-Band-Strap-Bracelet-For-Fitbit-Surge/142736041528?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/New-Tenor-Trombone-Soft-Gig-Bag-Case-Double-Aglet-Design-Black/131172183606?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/2-Technical-Pro-8-Studio-Monitor-Bookshelf-Computer-Multimedia-Speaker-Stands/302699624327?_trkparms=5373%3A0%7C5374%3AFeatured 
     has no ratings
    https://www.ebay.com/itm/T-Rex-Attack-Moose-Long-Sleeve-Crew-Neck-Ugly-Christmas-Sweater-Funny-Shirt/311771701980?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003158 
     has no ratings
    https://www.ebay.com/itm/PUMA-Prowl-Alt-2-Wns-Women-Shoe-Training-New/283119405668?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003158 
     has no ratings
    https://www.ebay.com/itm/Boston-Red-Sox-2018-World-Series-Champs-Framed-15-x-17-Collage-Fanatics/332857730287?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003158 
     has no ratings
    https://www.ebay.com/itm/Apple-Watch-Series-4-BRAND-NEW-40MM-GPS-WiFi-Bluetooth-1-Year-Warranty/163329295797?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004312 
     has no ratings
    https://www.ebay.com/itm/PUMA-Ferrari-Drift-Cat-5-Ultra-Sneakers-Men-Shoe-Auto-New/283142235605?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004312 
     has no ratings
    https://www.ebay.com/itm/Samsung-Galaxy-S9-SM-G960U-64GB-Sprint-GSM-Unlocked-Android-Smartphone/253866848031?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000001691 
     has no ratings
    https://www.ebay.com/itm/Dell-Alienware-AW3418DW-34-Curved-WQHD-4ms-120Hz-NVIDIA-G-Sync-Gaming-Monitor/382291335699?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000001691 
     has no ratings
    https://www.ebay.com/itm/Samsung-QN55Q8FN-2018-55-Smart-Q-LED-4K-Ultra-HD-TV-with-HDR-QLED/392017180265?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000001691 
     has no ratings
    https://www.ebay.com/itm/Magic-Chef-MCM770B-0-7-Cubic-ft-700-Watt-Microwave-with-Digital-Touch/292439222629?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003710 
     has no ratings
    https://www.ebay.com/itm/Coffee-K-Cup-Sampler-Packs-Choose-from-Bold-Flavored-Reg-or-Party-Mix-80-Ct/253926307354?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003710 
     has no ratings
    https://www.ebay.com/itm/LOL-Surprise-Collector-Bundle-5-Pack-Glitter-Pet-SideKick-Mermaid-L-O-L-CHOP/264020186298?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004297 
     has no ratings
    https://www.ebay.com/itm/Mega-Construx-Call-of-Duty-Urban-Assault-Copter-Building-Set-Bloks-FDY78-CHOP/263960214549?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004297 
     has no ratings
    https://www.ebay.com/itm/Columbia-Mens-Tech-Pine-Ridge-Half-Zip-Jacket/332668511616?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004299 
     has no ratings
    https://www.ebay.com/itm/Alpine-Swiss-RFID-Blocking-ID-Card-Holder-Thin-Minimalist-Front-Pocket-Wallet/362360655165?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004299 
     has no ratings
    https://www.ebay.com/itm/KitchenAid-Stand-Mixer-tilt-5-QT-rrk150-Refurb-Of-Ksm150ps-Artisan-Tilt-ALLMetal/361100492861?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004296 
     has no ratings
    https://www.ebay.com/itm/Quaker-Instant-Oatmeal-Cinnamon-Spice-Flavor-10-Packet-Boxes-12-Boxes/382552224064?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004296 
     has no ratings
    https://www.ebay.com/itm/Alpine-Swiss-Pierina-Womens-Ballet-Flats-Leather-Lined-Classic-Slip-On-Shoes/362142649765?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004302 
     has no ratings
    https://www.ebay.com/itm/Under-Armour-Womens-UA-Tech-V-Neck-T-Shirt/332722636335?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004302 
     has no ratings
    https://www.ebay.com/itm/LG-OLED55B8PUA-55-Smart-OLED-4K-Ultra-HD-TV-with-HDR/253960494788?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004217 
     has no ratings
    https://www.ebay.com/itm/LG-OLED55E8P-55-4K-HDR-Smart-AI-OLED-TV-w-ThinQ-OLED55E8PUA/253507399272?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004217 
     has no ratings
    https://www.ebay.com/itm/Natures-Best-ZERO-CARB-ISOPURE-3lbs-100-Whey-Isolate-Protein-PICK-FLAVOR/290926068421?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004306 
     has no ratings
    https://www.ebay.com/itm/Samsung-Galaxy-Note-9-128GB-SM-N9600-FACTORY-UNLOCKED-6-4-Snapdragon-845/132742920393?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003253 
     has no ratings
    https://www.ebay.com/itm/Apple-iPhone-7-a1778-32GB-GSM-Unlocked/252816011949?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003253 
     has no ratings
    https://www.ebay.com/itm/Fuelworx-Made-in-the-USA-Stackable-Easy-Pour-Gas-Can-CARB-Compliant-2-5-Gallon/173447172748?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000000126 
     has no ratings
    https://www.ebay.com/itm/MSI-GeForce-RTX-2080-GAMING-X-TRIO-Video-Card-8GB-256-Bit-GDDR6/382579978539?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004300 
     has no ratings
    https://www.ebay.com/itm/SPECIAL-PRICE-BANK-WIRE-PAYMENT-1-oz-Gold-Buffalo-BU-Random-Year-Lot-of-10/142685356389?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000000123 
     has no ratings
    https://www.ebay.com/itm/2018-Mexico-5-oz-Silver-Libertad-BU-SKU-162409/142741065805?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000000123 
     has no ratings
    https://www.ebay.com/itm/SPECIAL-PRICE-BANK-WIRE-2019-GB-1-oz-Gold-Queens-Beasts-The-Falcon-Lot-of-10/113347258206?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000000123 
     has no ratings
    https://www.ebay.com/itm/New-Era-5950-Boston-Red-Sox-2017-GAME-Low-Profile-Fitted-Hat-Navy-MLB-Cap/132268189248?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004236 
     has no ratings
    https://www.ebay.com/itm/Colin-Kaepernick-NFL-San-Francisco-49ers-Mid-Tier-Home-Red-Jersey-Youth-S-XL/222915128521?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004236 
     has no ratings
    https://www.ebay.com/itm/NFL-Mid-Tier-Home-Away-Team-Player-Official-Jersey-Collection-Youth-S-XL/392013497544?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004236 
     has no ratings
    https://www.ebay.com/itm/Under-Armour-Womens-Power-Performance-Jersey-T-Shirt/332741320953?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004236 
     has no ratings
    https://www.ebay.com/itm/USA-Bright-12V-5M-16-4ft-3528-5050-5630-RGB-SMD-300-LED-Flexible-Strip-light/301681651381?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004007 
     has no ratings
    https://www.ebay.com/itm/Outdoor-Colorful-Snowflake-LED-Laser-Light-Projector-Landscape-Xmas-Garden-Party/292735498676?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000004007 
     has no ratings
    https://www.ebay.com/itm/MLB-Majestic-Player-Name-Number-Jersey-T-Shirt-Collection-Youth-Size-8-20/272785398880?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000002859 
     has no ratings
    https://www.ebay.com/itm/Alpine-Swiss-Men-s-Gloves-Dressy-Genuine-Leather-Warm-Thermal-Lined-Wrist-Strap/311733447856?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000002859 
     has no ratings
    https://www.ebay.com/itm/Samsung-Galaxy-S8-SM-G950U-64GB-GSM-Unlocked-Android-Smartphone-Shadow-LCD/142914496069?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003712 
     has no ratings
    https://www.ebay.com/itm/Mini-Pedal-Stepper-Bike-Leg-Arm-Cycling-Fitness-Exerciser-Trainer-LCD-Display/142470574663?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003712 
     has no ratings
    https://www.ebay.com/itm/Apple-iPhone-X-a1901-256GB-Smartphone-GSM-Unlocked/152924515886?_trkparms=5373%3A0%7C5374%3AFeatured%7C5079%3A6000003712 
     has no ratings



```python
ratings
```




    ['4.7',
     None,
     None,
     None,
     '4.9',
     '4.5',
     None,
     '4.9',
     None,
     '5.0',
     None,
     None,
     '4.5',
     None,
     '4.9',
     None,
     None,
     None,
     None,
     '4.9',
     '4.7',
     None,
     '5.0',
     None,
     None,
     '4.1',
     None,
     None,
     None,
     '4.7',
     None,
     '4.0',
     '5.0',
     None,
     '4.5',
     None,
     None,
     None,
     '4.5',
     None,
     '3.3',
     '4.7',
     None,
     '4.7',
     None,
     None,
     '4.5',
     None,
     None,
     '4.8',
     '5.0',
     '4.7',
     '5.0',
     '5.0',
     None,
     '4.6',
     None,
     None,
     None,
     '5.0',
     None,
     '4.8',
     None,
     None,
     None,
     '4.9',
     None,
     '4.7',
     None,
     '4.8',
     '5.0',
     '4.5',
     '4.6',
     '5.0',
     '4.7',
     None,
     None,
     '4.9',
     None,
     '4.0',
     '4.9',
     None,
     '4.0',
     None,
     '4.4',
     None,
     None,
     '5.0',
     None,
     '5.0',
     '4.9',
     None,
     '5.0',
     None,
     '4.8',
     '4.8',
     '4.7',
     None,
     '4.8',
     '4.7',
     None,
     None,
     '4.5',
     None,
     '4.4',
     '5.0',
     None,
     '4.9',
     '4.7',
     '5.0',
     None,
     None,
     None,
     '5.0',
     '5.0',
     '4.7',
     '4.6',
     '3.8',
     None,
     None,
     None,
     None,
     '4.3',
     '3.4',
     None,
     None,
     '4.8',
     None,
     None,
     '4.6',
     None,
     None,
     None,
     '5.0']



## Synthesizing
Create a script using your two functions above to scrape all of the song lyrics for a given artist.



```python
#Use this block for your code!
song_urls = []
for link in data:
    try:
        url_song = 'https://www.azlyrics.com'+link[1][2:]
        song_urls.append(url_song)
    except:
        print(link)
```

    ('', None, 'EP: "Elliphant" (2012)')
    ('', None, 'album: "A Good Idea" (2013)')
    ('', None, 'EP: "Look Like You Love It" (2014)')
    ('', None, 'EP: "One More" (2014)')



```python
import xml.etree.ElementTree as ET
for song_url in song_urls:
    print(song_url)
    r = requests.get(song_url)
    song_soup = BeautifulSoup(r.text, 'html.parser')
    print(str(song_soup.find_all("meta",attrs={"name":"description"})[0]))
    print("\n\n")
```

    https://www.azlyrics.com/lyrics/elliphant/downonlife.html
    <meta content='Lyrics to "Down On Life" song by Elliphant: We are waking up in a pile of shit The whole bay is full of it And eggs keep growing out of our ears...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/tekknoscene.html
    <meta content='Lyrics to "Tekkno Scene" song by Elliphant: Said this could be a Color crusher Color rusher Touch this tune see See me sala Bim bim be Flush thi...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/makeitjuicy.html
    <meta content='Lyrics to "Make It Juicy" song by Elliphant: Come here Lucifer the sober soul me offer Run down pop gonâ all cracky color Come, come, come here c...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/inthejungle.html
    <meta content='Lyrics to "In The Jungle" song by Elliphant: Sick a in the jungle back to instinct back to Basic a runk a back to basic instinct cashing life in...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/cianthearit.html
    <meta content="Lyrics to &quot;Ciant Hear It&quot; song by Elliphant: Ciant hear it A dripping spirit coming up Coming from the deep he's coming closer don't stop One mo..." name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/cianthearitjungleremix.html
    <meta content="Lyrics to &quot;Ciant Hear It (Jungle Remix)&quot; song by Elliphant: A dripping spirit coming up Coming from the deep he's coming closer don't stop One more day after th..." name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/downonlife.html
    <meta content='Lyrics to "Down On Life" song by Elliphant: We are waking up in a pile of shit The whole bay is full of it And eggs keep growing out of our ears...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/downonlife.html
    <meta content='Lyrics to "Down On Life" song by Elliphant: We are waking up in a pile of shit The whole bay is full of it And eggs keep growing out of our ears...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/musicislife.html
    <meta content='Lyrics to "Music Is Life" song by Elliphant: Music is life, life, life, life Come again E-e-elliphant Holla at me hater Imma holla back See me c...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/livetillidie.html
    <meta content='Lyrics to "Live Till I Die" song by Elliphant: Let go, all you propha cool People, drop your mask evend Your seakhole, set your blood flow in Moseo...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/coulditbe.html
    <meta content='Lyrics to "Could It Be" song by Elliphant: Somethingâs been growing In my kitchen I been scared for weeks Something make noises I canât sleep m...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/whereismymamaat.html
    <meta content="Lyrics to &quot;Where Is My Mama At&quot; song by Elliphant: Where is my mama at I need my baseball bat I need the baseball bat Don't forget, how easy you forget..." name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/shootmedown.html
    <meta content="Lyrics to &quot;Shoot Me Down&quot; song by Elliphant: I need a surprise If you a freak it would be nice Shut up or be a liar Don't care 'bout your name th..." name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/toiletlineromance.html
    <meta content='Lyrics to "Toilet Line Romance" song by Elliphant: Said Iâm a sucker of it na cook a rock You lucky son of a sun and moon fuck I run down rivers wild o...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/downonlife.html
    <meta content='Lyrics to "Down On Life" song by Elliphant: We are waking up in a pile of shit The whole bay is full of it And eggs keep growing out of our ears...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/runfar.html
    <meta content='Lyrics to "Run Far" song by Elliphant: I really just wanna get the hell out of the fucking place Haha! Zip it, zip it, zip it God damn this...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/morefire.html
    <meta content='Lyrics to "More Fire" song by Elliphant: Can I, can I, can I talk right now Na missy busy jumping, busy jumping around Missy breaking down, b...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/boomyourhead.html
    <meta content='Lyrics to "Boom Your Head" song by Elliphant: Boom your head up Boom boom boom your head up Boom your head up May be bit hairy and my boobs air...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/painttheworld.html
    <meta content='Lyrics to "Paint The World" song by Elliphant: It was a sunny Afternoon you confronted me It was a sunny, a sunny Sunday I had a feeling you go som...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/wantit.html
    <meta content='Lyrics to "Want It" song by Elliphant: Got some funky functions worth mention, man Got that thick feeling no more lonesome Come with good c...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/pacman.html
    <meta content='Lyrics to "Pac Man" song by Elliphant: (Jungle) Love Love, love Gotta lay low but hold you high Feet on the ground nose dip in the sky I...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/pompom.html
    <meta content='Lyrics to "Pom Pom" song by Elliphant: Life is, coming in, was here, very fast to be passing, constantly changing, but it was here, to bloo...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/downonlife.html
    <meta content='Lyrics to "Down On Life" song by Elliphant: We are waking up in a pile of shit The whole bay is full of it And eggs keep growing out of our ears...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/looklikeyouloveit.html
    <meta content="Lyrics to &quot;Look Like You Love It&quot; song by Elliphant: Bring it up, me can't hear it A call full of full of blasta, we gon' share it Run for life, you can..." name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/allornothing.html
    <meta content='Lyrics to "All Or Nothing" song by Elliphant: See me sadness, when me love Let me quire in me work Feel me love and want me bitch Pull me switch,...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/revolusion.html
    <meta content="Lyrics to &quot;Revolusion&quot; song by Elliphant: My surroundings taint my brain I'm just a city kid who fought a lot Just one distraction will show t..." name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/everything4u.html
    <meta content="Lyrics to &quot;Everything 4 U&quot; song by Elliphant: I'm a gazelle and you're a lion I heard the bells but I ignored them What could be worth more Than t..." name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/onlygettingyounger.html
    <meta content="Lyrics to &quot;Only Getting Younger&quot; song by Elliphant: Why your skin go wrinkle we only getting younger We ride into a peek we can't take this shit no long..." name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/bootykillah.html
    <meta content="Lyrics to &quot;Booty Killah&quot; song by Elliphant: I give you tender fun, a natural and high Hear 'em talkin', &quot;What? And what's them rumours about?&quot; C..." name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/downonlife.html
    <meta content='Lyrics to "Down On Life" song by Elliphant: We are waking up in a pile of shit The whole bay is full of it And eggs keep growing out of our ears...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/onemore.html
    <meta content="Lyrics to &quot;One More&quot; song by Elliphant: Come on na sugar, come I really don't wanna go home Stay with me, be a friend These streets so cold..." name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/purplelight.html
    <meta content='Lyrics to "Purple Light" song by Elliphant: Reaching up, for the glass Come on na, fill it up Been empty for some time, come on fill me up Crack...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/neverbeeninlove.html
    <meta content="Lyrics to &quot;Never Been In Love&quot; song by Elliphant: Summer's gone and I am broke man And my bills don't wait Mommy's angry, club is all dead I'm born..." name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/savethegrey.html
    <meta content="Lyrics to &quot;Save The Grey&quot; song by Elliphant: Man is my dirty and each made clean, STG Look into the eyes of them we pee sincere, STG Mama's not f..." name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/youregone.html
    <meta content="Lyrics to &quot;You're Gone&quot; song by Elliphant: Who's gonna make sure mind is not dirty then Who's gonna help me get my shit back when I'm shit face..." name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/onemore.html
    <meta content="Lyrics to &quot;One More&quot; song by Elliphant: Come on na sugar, come I really don't wanna go home Stay with me, be a friend These streets so cold..." name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/stepdown.html
    <meta content="Lyrics to &quot;Step Down&quot; song by Elliphant: I'm home, home alone I call, call again you not answering And I know You couldn't, you wouldn't dare..." name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/everybody.html
    <meta content='Lyrics to "Everybody" song by Elliphant: Everybody got a nine Everybody is a bad boy now Everybody wanna shine Everybody drink your champagne...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/lovemebadder.html
    <meta content="Lyrics to &quot;Love Me Badder&quot; song by Elliphant: My pillow smells like your cologne I just can't wait 'til you get home (ra-ah) You gangsta ways turn..." name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/notready.html
    <meta content='Lyrics to "Not Ready" song by Elliphant: We broke this open we shared the blisset things We saw too much now we see nothing You filled me emp...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/lovemelong.html
    <meta content='Lyrics to "Love Me Long" song by Elliphant: People on the street Used to be you and me One bullet in your gun Put it on you and me (Yeah, yeah,...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/hitandrun.html
    <meta content="Lyrics to &quot;Hit And Run&quot; song by Elliphant: Everything feel different now Home is gone I can't find it now Kept it all in the inside now Next to..." name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/thingcalledlife.html
    <meta content="Lyrics to &quot;Thing Called Life&quot; song by Elliphant: It's not hard to see What time done to you It's okay to me What time make you do It's about the tim..." name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/whereishome.html
    <meta content="Lyrics to &quot;Where Is Home&quot; song by Elliphant: When the way come you know we can't stay And then is calling out for me And you flashes pink and li..." name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/onemore.html
    <meta content="Lyrics to &quot;One More&quot; song by Elliphant: Come on na sugar, come I really don't wanna go home Stay with me, be a friend These streets so cold..." name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/playerrun.html
    <meta content='Lyrics to "Player Run" song by Elliphant: Coming home from duty, I need, I need a break This been so and I feel so low Calling up for the cit...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/spoonme.html
    <meta content='Lyrics to "Spoon Me" song by Elliphant: Sipping bad habits in the break of day Trip on fast lovers like come get with me Looking damn hard t...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/livinglifegolden.html
    <meta content="Lyrics to &quot;Living Life Golden&quot; song by Elliphant: We fly in space, one numbers Leaving no trace, my hair a billion wonders Now I'm riding away, the m..." name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/bitchout.html
    <meta content='Lyrics to "Bitch Out" song by Elliphant: Letâs make another random track na Zip it Yeah yeah Yeah I come around look dope Yeah you come aro...' name="description"/>
    
    
    
    https://www.azlyrics.com/lyrics/elliphant/northstarbloodychristmas.html
    <meta content="Lyrics to &quot;North Star (Bloody Christmas)&quot; song by Elliphant: Mmm Bloody Christmas, happy drunker Shattered wishes, I'm glad we meet again Lonely white stream, em..." name="description"/>
    
    
    


## Visualizing
Generate two bar graphs to compare lyrical changes for the artist of your chose. For example, the two bar charts could compare the lyrics for two different songs or two different albums.


```python
#Use this block for your code!
```

## Level - Up

Think about how you structured the data from your web scraper. Did you scrape the entire song lyrics verbatim? Did you simply store the words and their frequency counts, or did you do something else entirely? List out a few different options for how you could have stored this data. What are advantages and disadvantages of each? Be specific and think about what sort of analyses each representation would lend itself to.


```python
#Use this block for your code!
```

## Summary

Congratulations! You've now practiced your Beautiful Soup knowledge!
