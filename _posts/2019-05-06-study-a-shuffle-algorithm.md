---
layout: post
title:  洗牌算法原理及实现
date:   2019-05-06 23:40:00 +0800
categories: study-a-shuffle-algorithm
---

> 洗牌算法分为抽牌、换牌和插牌三种，其中抽牌对应 Fisher-Yates 算法，换牌对应 Knuth-Durstenfeld 算法[1]。

## 1. 要点
+ 抽牌对应 Fisher-Yates 算法
+ 换牌对应 Knuth-Durstenfeld 算法


## 2. 原理

### 2.1. Fisher-Yates Shffule

### 2.2. Knuth-Durstenfeld Shuffle

代码如下：
```go
func (this *Game) Shuffle(cards []Card, seed int) []Card {
	rand.Seed(time.Now().Unix() ^ (int64(seed)<<32 | int64(seed)))
	for i := len(cards); i > 0; i-- {
		r := rand.Intn(i)
		cards[r], cards[i-1] = cards[i-1], cards[r]
	}
	return cards
}
```

功能测试如下：
```go
func (this *Game) Shuffle(cards []Card, seed int) []Card {
	rand.Seed(time.Now().Unix() ^ (int64(seed)<<32 | int64(seed)))
	for i := len(cards); i > 0; i-- {
		r := rand.Intn(i)
		cards[r], cards[i-1] = cards[i-1], cards[r]
	}
	return cards
}
```

分布特性测试如下：
```go
func TestGame_Shufflt_Distribution(t *testing.T) {
	game, err := cardgame.New()
	if err != nil {
		t.Error(err.Error())
	}
	
	count := make([][]int, 10)
	for k := 0; k < 10; k++ {
		count[k] = make([]int, 10)
	}
	
	for t := 0; t < 100000; t++ {
		// create all cards
		allCards := make([]cardgame.Card, 10)
		for i := 0; i < 10; i++ {
			allCards[i] = cardgame.Card{Value:i, Suit:0}
		}
		// shuffle
		shuffledCards := game.Shuffle(allCards, t)
		// count
		for j := 0; j < 10; j++ {
			card := shuffledCards[j]
			count[j][card.Value] = count[j][card.Value] + 1
		}
	}
	
	fmt.Println(count)
}
```

## A. 参考
1. [三种洗牌算法shuffle](https://blog.csdn.net/qq_26399665/article/details/79831490)