# Assignment3
 Ecommerce Buy-sale store การสร้างเว็บขายของ เพิ่มสินค้าแบบง่ายๆ เพื่อเป็นการสร้าง DApp นำมารวมกับการทำงานของ blockchian ในรูปแบบของการขายซื้อ
![](picture/สกรีนช็อต%202021-01-13%20191246.png)
ขั้นตอนในส่วนนี้ก็จะมีดังนี้<br />
<br />
1.ติดตั้ง Truffle และ Code Editor <br />
2.สร้าง Project ใหม่ด้วย Truffle<br />
3.เขียน Smart Contract<br />
4.Compile Smart Contract ด้วย Truffle<br />
โปรแกรมที่ต้องมี<br />
Virual box สำหรับการจำลองการทำงานของ ubuntu , ubuntu ,truffle,meta mask,ganache,insomnia <br />
เมื่อติดตั้งโปรแกรมทั้งหมดเรียบร้อยแล้ว<br />
**สำหรับ Smart Contract นี้ ประกอบด้วย 3 ฟังก์ชันด้วยกัน** คือ<br />

ฟังก์ชันเพิ่มสินค้าในฐานข้อมูล<br />
ฟังก์ชันรับรายละเอียดของสินค้า<br />
ฟังก์ชันสั่งซื้อสินค้า<br />
**#เริ่มการเตรียม Code editor**<br />
เมื่อติดตั้ง Truffle เสร็จแล้ว ขั้นตอนต่อมาจะเป็นการเขียนโปรแกรมเตรียมไว้ โดยใช้โปรแกรม Visual studio สามารถโหลดได้จาก softwareappilcation ใน Ubuntu หรือ โดยตรงผ่านบราวเซอร์
ภาษาที่ใช้เขียนควรรู้ มี  Solidity,javascript,html เป็นต้น <br />

สร้างโฟรเดอร์สำหรับงานใช้คำสั่งผ่าน commandline <br />

```mkdir (name of work) example mkdir simple ecomerce dapp``` <br />

เสร็จแล้วตามด้วยการเข้าไปในโฟรเดอร์งานที่เพิ่งสร้าง<br />

```cd (name of work) example cd mkdir simple ecomerce dapp ```<br />


>![](picture/สกรีนช็อต%202021-01-14%20000858.png)<br />

และสุดท้าย ก็เริ่มโปรเจค Truffle ด้วยคำสั่ง<br />

```truffle init```<br />
>![](picture/สกรีนช็อต%202021-01-14%20001904.png)<br />


เมื่อเข้าไปดูที่โฟลเดอร์ จะมีโฟลเดอร์และไฟล์ดังนี้<br />
├── contracts<br />
├── migrations<br />
├── test<br />
└── truffle-config.js<br />

**#Implement smart contracts**<br />
เริ่มเขียน Smartcontract สร้างไฟล์ชื่อ Token.sol ในโฟลเดอร์ contracts โดยให้ในไฟล์มีโค้ดดังต่อไปนี้ <br />
```
pragma solidity >=0.4.25 <0.6.0;

contract Token {
    mapping (address => uint256) public balanceOf;

    constructor(uint256 initialSupply) public {
        balanceOf[msg.sender] = initialSupply;
    }

    function transfer(address _from, address _to, uint256 _value) public returns (bool success) {
        require(balanceOf[_from] >= _value);                // Check if the sender has enough
        require(balanceOf[_to] + _value >= balanceOf[_to]); // Check for overflows
        balanceOf[_from] -= _value;
        balanceOf[_to] += _value;
        return true;
    }
}

```
>เสร็จแล้ว ให้สร้าง Smart Contract สำหรับระบบ Ecommerce ของเรา โดยสร้างไฟล์ชื่อ Shop.sol ในโฟลเดอร์เดียวกัน<br />
ประกอบด้วย 3 ฟังก์ชันด้วยกัน คือ

ฟังก์ชันเพิ่มสินค้าในฐานข้อมูล
ฟังก์ชันรับรายละเอียดของสินค้า
ฟังก์ชันสั่งซื้อสินค้า
```
pragma solidity >=0.4.25 <0.6.0;

import "./Token.sol";

contract Shop {
    struct Product {
        string name;
        string imgPath;
        uint256 price;
        uint256 quantity;
        address seller;
    }
    event AddedProduct(uint256 pid, address seller, uint256 timestamp);
    event BuyProduct(uint256 pid, address buyer, uint256 timestamp);
    mapping (uint256 => Product) products;
    mapping (uint256 => address[]) buying;
    Token token;

    constructor (address _tokenAddress) public {
        token = Token(_tokenAddress);
    }

    function addProduct(
        uint256 _pid,
        string memory _name,
        uint256 _price,
        uint256 _quantity,
        string memory _imgPath,
        uint256 timestamp
    ) public {
        products[_pid] = Product({
            name: _name,
            imgPath: _imgPath,
            price: _price,
            quantity: _quantity,
            seller: msg.sender
        });
        emit AddedProduct(_pid, msg.sender, timestamp);
    }

    function getProduct(uint256 _pid) public view returns (string memory name, uint256 price, uint256 quantity, string memory imgPath, address seller) {
        Product memory product = products[_pid];
        return (product.name, product.price, product.quantity, product.imgPath, product.seller);
    }

    function buyProduct(uint256 _pid, uint256 _timestamp) public {
        require(products[_pid].quantity > 0, "Product is sold out");

        Product storage product = products[_pid];
        address _buyer = msg.sender;
        token.transfer(_buyer, product.seller, product.price);

        product.quantity -= 1;

        buying[_pid].push(_buyer);
        emit BuyProduct(_pid, _buyer, _timestamp);
    }
}
```
> **#Compile smart contracts**<br />
```truffle compile```<br />

จะแสดงผลออกมาเป็นแบบนี้ <br />
```
Compiling your contracts...
===========================
> Compiling ./contracts/Migrations.sol
> Compiling ./contracts/Shop.sol
> Compiling ./contracts/Token.sol
> Artifacts written to /home/icegotcha/Data/truffle-example/simple_ecommerce_contract/build/contracts
> Compiled successfully using:
   - solc: 0.5.0+commit.1d4f565a.Emscripten.clang
   ```
   <br />

>![](picture/สกรีนช็อต%202021-01-14%20003031.png)<br />

**#Create Migration Scripts** <br />
เข้าไปในโฟรเดอร์ Migartions สร้างไฟล์ชื่อ 2_deploy_contracts.js
สำหรับการ Deploy Smart Contract ที่เขียนขึ้น สามารถเขียน Migration ได้ดังนี้<br />
 ```
 const Token = artifacts.require('Token');
const Shop = artifacts.require('Shop');

module.exports = function(deployer, networks, accounts) {
  deployer
    .deploy(Token, 10000000)
    .then(async () => {
      const tokenContract = await Token.deployed();
      return deployer.deploy(Shop, tokenContract.address);
    })
    .then(async () => {
      const token = await Token.deployed();
      const coinbase = accounts[0];
      const value = 500;
      await token.transfer(coinbase, accounts[1], value);
    });
};
```
>![](picture/สกรีนช็อต%202021-01-14%20003927.png)<br />
ใน Truffle เราสามารถรัน Migration ได้ในคำสั่งเดียว คือ 
```truffle migrate``` <br/>
**#Config Ethereum Networks** <br />
ก่อนจะที่ migrate truffle ควรจะเริ่มการเชื่อมต่อ Ethreum เปิดโปรแกรม ganache สร้าง Workspace ชื่อที่เราตั้งขึ้นมา จะได้ตามรูปนี้<br />
สังเกต Network id,RPC server เราจะนำไปใช้สำหรับการเชื่อมต่อกับ Meta mask<br />

>![](picture/สกรีนช็อต%202021-01-13%20192506.png)<br />

**Meta mask**<br />

มีการตั้งค่า Network ตามนี้<br />

>![](picture/สกรีนช็อต%202021-01-14%20005224.png<br />
 
จากนั้นเริ่มการ migrate ได้เลย ระวังการเชื่อมต่อ IP ควรจะเป็น 7454 เข้าไปเช็คในไฟล์ต่าง ๆในเรียบร้อย <br />

**Create Main Script**<br />
สร้างไฟล์แรกชื่อ index.js ไฟล์นี้จะเป็นตัวเริ่มทำงานของแอปพลิเคชัน  ภายในจะเรียกใช้ฟังก์ชัน addProduct, buyProduct, 
และ getProducts และ Event AddedProduct ตามลำดับในSmart Contract shop <br />
```
const fs = require('fs');
const path = require('path');
const Multer = require('multer');
const express = require('express');
const bodyParser = require('body-parser');
const {
  addProduct,
  buyProduct,
  getProduct,
  getAccounts,
  unlockAccount,
  getAllProducts,
} = require('./blockchain');

// IMAGE UPLOADER SESSING
const upload = Multer({ dest: 'public/images/', limits: { files: 1 } });
const MAX_IMAGE_SIZE_BYTES = 10485760;

const app = express();
const appPort = 3000;

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));

app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');

const bootstrapJS = express.static(path.join(__dirname, 'node_modules/bootstrap/dist/js/bootstrap.min.js'));
const bootstrapCSS = express.static(path.join(__dirname, 'node_modules/bootstrap/dist/css/bootstrap.min.css'));
const toastrCSS = express.static(path.join(__dirname, 'node_modules/toastr/build/toastr.min.css'));
const toastrJS = express.static(path.join(__dirname, 'node_modules/toastr/build/toastr.min.js'));
const jquery = express.static(path.join(__dirname, 'node_modules/jquery/dist/jquery.min.js'));
const holderjs = express.static(path.join(__dirname, 'node_modules/holderjs/holder.min.js'));

app.use('/js/jquery.min.js', jquery);
app.use('/js/holder.min.js', holderjs);
app.use('/js/bootstrap.min.js', bootstrapJS);
app.use('/css/bootstrap.min.css', bootstrapCSS);
app.use('/js/toastr.min.js', toastrJS);
app.use('/css/toastr.min.css', toastrCSS);
app.use(express.static(path.join(__dirname, 'public')));

app.get('/', async (request, response, next) => {
  const accounts = await getAccounts();
  const products = await Promise.all((await getAllProducts()).map(pid => getProduct(pid)));
  return response.render('index', { accounts, products });
});

app.post('/products/add', upload.single('productImageInput'), async (request, response) => {
  try {
    const { productNameInput, productPriceInput, productQtyInput, productSeller, accountPassword } = request.body;
    const { file } = request;
    if (!['image/jpeg', 'image/png', 'image/gif'].includes(file.mimetype)) {
      fs.unlinkSync(file.path);
      return response.json({
        success: false,
        error: 'Please upload a file as jpeg, png, or gif.',
      });
    }

    if (file.size > MAX_IMAGE_SIZE_BYTES) {
      fs.unlinkSync(file.path);
      return response.json({
        success: false,
        error: 'Please upload smaller file. (10MB)',
      });
    }

    if (!productNameInput || !productPriceInput || !productQtyInput || !productSeller) {
      return response.json({
        success: false,
        error: 'Please fill the form.',
      });
    }

    if (!Number.isInteger(Number(productPriceInput))) {
      return response.json({
        success: false,
        error: 'Plese enter product price in number',
      });
    }

    if (!Number.isInteger(Number(productQtyInput))) {
      return response.json({
        success: false,
        error: 'Plese enter product quantity in number',
      });
    }

    const productImagePath = 'images/' + file.filename;
    const unlocked = await unlockAccount(productSeller, accountPassword);
    if (!unlocked) {
      return response.json({
        success: false,
        error: 'Please type correct account password.',
      });
    }
    const dataResult = await addProduct(productNameInput, productPriceInput, productQtyInput, productImagePath, productSeller);
    return response.json({
      success: true,
      data: { pid: dataResult.pid, transactionReceipt: dataResult.receipt },
      error: null,
    });
  } catch (error) {
    return response.json({
      success: false,
      error: error.message,
    });
  }
});

app.post('/products/buy', async (request, response) => {
  try {
    const { pid, buyer, password } = request.body;
    if (!pid) {
      return response.json({
        success: false,
        error: 'Please select product ID.',
      });
    }

    if (!buyer) {
      return response.json({
        success: false,
        error: 'Please select address to be buyer.',
      });
    }

    const unlocked = await unlockAccount(buyer, password);
    if (!unlocked) {
      return response.json({
        success: false,
        error: 'Please type correct account password.',
      });
    }

    await buyProduct(pid, buyer);
    return response.json({
      success: true,
      error: null,
    });
  } catch (error) {
    return response.json({
      success: false,
      error: error.message,
    });
  }
});

app.post('/products/:pid', async (request, response) => {
  try {
    	const { pid } = request.params;
      const result = await getProduct(pid);
      return response.json({
         success: true,
         data: result,
         error: null,
      });
  }
  catch (error) {
    return response.json({
      success: false,
      error: error.message,
    });
  }
});

app.listen(appPort, () => console.log(`Ecommerce server is running! it is listening on port ${appPort}...`));
```
<br />
เสร็จแล้ว ลองให้ทำงานโดยพิมพ์คำสั่งนี้ลงใน Command Line ```npm start```<br />

**#Connect to Ethereum with Web3**<br />
การเชื่อมต่อไปที่ Ethereum นั่นเอง ซึ่งการติดต่อจากแอปพลิเคชัน สามารถใช้ Package ชื่อว่า web3
สร้างไฟล์ชื่อว่า blockchain.js และเรียกใช้ Package web3 แบบดังตัวอย่างนี้ <br />
```
onst fs = require('fs');
const path = require('path');
const Web3 = require('web3');
const TruffleContract = require('@truffle/contract');

const web3Provider = new Web3.providers.HttpProvider('http://localhost:7545');
const web3 = new Web3(web3Provider);

const createContractInstance = async artifactName => {
 const artifact = JSON.parse(fs.readFileSync(path.join(__dirname, 'build/contracts', `${artifactName}.json`)));
  const contract = TruffleContract(artifact);
  contract.setProvider(web3Provider);
  return contract.deployed();
};

let shop;
createContractInstance('Shop').then(instance => {
  shop = instance;
});

const addProduct = async (name, price, quantity, imgPath, seller) => {
  const timestamp = Date.now();
  const pid = timestamp;
  const receipt = await shop.addProduct(pid, name, price, quantity, imgPath, timestamp, { from: seller, gas: 1000000 });
  return { receipt: receipt, pid: pid };
};

const buyProduct = async (pid, buyer) => {
  try {
    const receipt = await shop.buyProduct(pid, Date.now(), { from: buyer, gas: 1000000 });
    return receipt;
  } catch (error) {
    if (error.reason) throw new Error(error.reason);
    throw error;
  }
};

const getProduct = async pid => {
  const product = await shop.getProduct.call(pid);
  product.id = pid;
  return product;
};

const getAllProducts = async () => {
  const events = await shop.getPastEvents('AddedProduct', { fromBlock: 0 });
  const allPids = events.map(item => item.returnValues.pid);
  return allPids;
};

const getAccounts = () => web3.eth.getAccounts();

const unlockAccount = (address, password) => web3.eth.personal.unlockAccount(address, password);

module.exports = {
 addProduct,
 buyProduct,
 getProduct,
 getAccounts,
 unlockAccount,
 getAllProducts,
}
```
เมื่อเสร็จแล้ว ก็ลองใช้โปรแกรม Insomnia ทดสอบ <br />
>![](picture/สกรีนช็อต%202021-01-13%20192751.png)<br />
จากนั้นสร้างไฟล์ .ejs 4 ไฟล์ในโฟลเดอร์ views ดังนี้
index.ejs,partial/add_product_modal.ejs,partial/buy_product_modal.ejs (ไฟล์ชื่อ buy_product_modal.ejs อยู่ในโฟลเดอร์ชื่อ partial อีกที),
partial/product_showcase.ejs,ขั้นตอนสุดท้าย ให้สร้างไฟล์ custom.js อยู่ในโฟลเดอร์ public/js<br />
จากนั้นเริ่มการ RUN <br />
``npm start```
<br />
จากนั้นเปิด Web Browser แล้วเข้าไปที่ URL http://localhost:3000<br />

>![](picture/สกรีนช็อต%202021-01-14%20011706.png)<br />















reference https://blockchain.fish/make-dapp-with-truffle-ch-1/
