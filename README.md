# Block-mining
## 블럭을 채굴 (mining)할때는 난위도와 시간을 조정하는데
### difficulty 생성방법

```
const Block_GNERATION_INTERVAL = 10 //	second 단위 :초 //블럭생성간격
const DIFFICULTY_ADJUSTMENT_INTERVAL = 10 // in blocks 블럭마다 난위도가 조정되는 간격 //블럭10개만들어질때마다 난위도조정
```
기대시간 = 초 * 난위도 (크거나작으면 특정시간을 조정해서 맞춤)

```
function getAdjustDifficulty(lastBlock, blocks){
	const prevAdjustmentBlcok = blocks[blocks.length - DIFFICULTY_ADJUSTMENT_INTERVAL];
	const elapsedTime = lastBlock.header.timestamp - prevAdjustmentBlcok.header.timestamp;//실제난위도 조정된간격
	const expectedTime = Block_GNERATION_INTERVAL * DIFFICULTY_ADJUSTMENT_INTERVAL;

	if (expectedTime / 2 > elapsedTime ){
		return prevAdjustmentBlcok.header.difficulty +1;//난위도올리기
	}
	else if (expectedTime *2 < elapsedTime){
		return prevAdjustmentBlcok.header.difficulty - 1;

	}
	else {
		return prevAdjustmentBlcok.header.difficulty;
	}
}
```
기대시간/2 보다 경과된 시간이 작으면 난위도를 올린다.

경과된시간이 기대시간/2 보다 크면 난위도를 내린다.

그외는 그냥 리턴

```
function getDIfficulty(blocks){
	const lastBlock = blocks[blocks.length - 1]
	if (lastBlock.header.index !==0 && 
		lastBlock.header.difficulty.index % DIFFICULTY_ADJUSTMENT_INTERVAL === 0){
		return getAdjustDifficulty(lastBlock,blocks);
	}
	return lastBlock.header.difficulty;
}
```
getDIfficulty함수

블럭의 헤더의 인덱스가 0 이고 (제네시스블럭)

블럭의 difficulty 인덱스를 DIFFICULTY_ADJUSTMENT_INTERVAL로 나눈 나머지가 0일때..

예를들어 지금 DIFFICULTY_ADJUSTMENT_INTERVAL가 10이니까 10배수...10,20,30...

return getAdjustDifficulty 위에 만든 함수로 난위도 조정

그외는 lastBlock.header.difficulty 리턴


### 타임도조정

시간구하는 함수

```
function getCurrentTimestamp(){
	return Math.round(Date().getTime() / 1000);
}
```
Math.round : 반올림

```
function isValidTimestamp(newBlock, prevBlock){
	if(newBlcok.header.timestamp - prevBlock.header.timestamp> 60)
		return false;
	if( getCurrentTimestamp() - newBlock.header.timestamp > 60)
		return false;

	return true;

}
```
isValidTimestamp 함수

newBlock.header.timestamp - prevBlock.header.timestamp 가 60보다 크면 조정

getCurrentTimestamp() - newBlock.header.timestamp 가 60보다 크면 조정

checkVaildBlock.js안에 이어서

isValidNewBlock 함수 안에

	///////////////////////////////////추가
	else if (!isValidTimestamp(newBlock,previousBlock)){
		console.log("Invalid Timestamp")
		return false;
	}

	else if(!hashMatchesDifficulty(createHash(newBlock),newBlock.header.difficulty)){
	console.log("Invalid hash")
	return false;
	}
	return true;
