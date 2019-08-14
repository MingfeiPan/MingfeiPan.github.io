---
layout: post
title: kmp的一些笔记
category: algorithm
tag : algorithm
---

读了读算法导论关于kmp章节的内容, 记录一下自己的理解。 

### 字符串匹配
 
字符串匹配ctrl+f可能每天都要用到个几十次, 所以理应了解一哈, 最直接就是暴力匹配, 挨个比较: 

```
func forceCompare(haystack string, needle string) int {
	var i, j int
	for i, j = 0, 0; i < len(haystack) && j < len(needle); {
		if haystack[i] == needle[j] {
			i++
			j++
		} else {
			i = i - j + 1
			j = 0
		}
	}
	if j == len(needle) {
		return i - j
	} else {
		return -1
	}
}
```

低效的O(mn)方法, 通常是要避免的, 我们可以看出在比较出现不同时的这一步是产生低效的原因: 

```
} else {
	i = i - j + 1
	j = 0
}
```

i 每次只能单步的增长, 而j更可怜要回到起点, 我们之前比较过的部分相同结果都被浪费了, 可否找到一个利用了这些被浪费的信息的算法来提高移动效率? 

### kmp

kmp是一个可以在O(n)时间复杂度下运行的字符串匹配算法, 他利用已匹配的信息加速移动, 观察下面场景
 

```
haystack: abdbcde
needle:   abc
```

比较到第三个字符时, 我们发现源字符串的a与目标字符串的c不匹配, 这时暴力解法的思路会将源字符串移动到第二位再开始比较, 但是仔细观察我们可以发现其实比较过了前三位都不用再比了, 我们其实可以直接移动到第四位开始新的比较

场景2: 

```
haystack: ababcde
needle:   abc

```

同样比较到第三个字符串, 我们发现了不匹配, 但是这里源字符串的第三个字符与目标字符串的第一个字符串是相同的, 这种情况如果你冒然移动前三位, 会导致错过haystack[2:5]这个答案, 所以结论: 

`我们可以利用这个已匹配信息提高效率, 但是要遵守一个规则, kmp给出了这种规则`

### 构造needle的匹配表 

我们需要利用needle的前缀子串(这些子串会在某个时刻成为已匹配信息)提供的信息来快速移动比较的位置, 其实从上面的例子中我们已经可以模糊看到要遵守的规则, 如果已匹配字符串中出现某些重叠部分, 也就是算法里描述的: 真后缀的最长前缀(非常绕口), 换个容易理解的说法叫做: 前缀与后缀的最长共有元素的长度


看算法导论书上的这个例子: 

给定needle P为ababaca

i | 1 | 2 | 3 | 4 | 5 | 6 | 7
:-: | :-: | :-: | :-: | :-: | :-: | :-: | :-:
P[i] | a | b | a | b| a | c | a
next[i] | 0| 0 | 1 | 2| 3 | 0 | 1 

了解下前缀, 后缀: 

> 前缀指除了最后一个字符外, 一个字符串的全部头部组合
> 后缀指除了第一个字符外, 一个字符串的全部尾部组合 


我们用前缀与后缀的最长共有元素长度规则来求这个表 

ababaca分别有子串: a, ab, aba, abab, ababa, ababac, ababaca

* a的前缀, 后缀都为空, 共有元素长度为0
* ab的前缀为[a], 后缀为[b], 共有元素长度为0 
* aba的前缀为[a, ab], 后缀为[ba, a], 最长共有元素a长度为1
* abab的前缀为[a, ab, aba], 后缀为[bab, ab, b], 最长共有元素ab长度为2
* ababa的前缀为[a, ab, aba, abab], 后缀为[baba, aba, ba, a], 最长共有元素aba长度为3
* ababac的前缀为[a, ab, aba, abab, ababa], 后缀为[babac, abac, bac, ac ,c], 最长共有元素长度为0
* ababaca的前缀为[a, ab, aba, abab, ababa, ababac], 后缀为[babaca, abaca, baca, aca, ca, a], 最长共有元素a长度为1

这样我们的next表为[0, 0, 1, 2, 3, 0, 1]

这种理解方式会更直观一些, 当然这种求解的方法是O(m^2)的, 并不建议

```
func getPrefixTable1(s string) []int {
	l := make([]int, len(s))
	var curStr string
	var curLen int
	for i := 1; i < len(s); i++ {
		fmt.Println(s[0 : i+1])
		curStr = s[0 : i+1]
		curLen = 0
		for j := 1; j < len(curStr); j++ {
			if s[0:j] == s[len(curStr)-j:len(curStr)] {
				if len(s[0:j]) > curLen {
					curLen = len(s[0:j])
				}
			}
		}
		l[i] = curLen
	}
	return l
}

```

下面看原装的next表求法, 其实就是自己做一次kmp: 

```
func getPrefixTable(s string) []int {
	l := make([]int, len(s))
	var j int
	for i := 1; i < len(s); {
		if s[i] == s[j] {
			//有匹配该位置匹配值+1
			l[i] = j + 1
			j++
			i++
		} else {
			if j > 0 {
				//无匹配缩小j到前一个值
				j = l[j-1]
			} else {
				l[i] = 0
				i++
			}
		}
	}
	return l
}
```

可以看整个思路是递推的逐级求解l[i]的值, 当目前匹配时, next[i]由前值+1; 而当目前不匹配时, 我们去找前一个的next[i-1]的值, 也就是找一个较短的相同前缀后缀串, 直到为0时得知此位置无匹配, 此时next[i]即为0。 

我们之前说过了next[i]代表的值k是表示needle[i]之前的子串中, 有长度为k的相同的前缀串跟后缀串。利用这个k值, 每当在needle[i]处匹配失败时, 下一步用k位置的字符继续匹配, 相当于字符串移动了i-k位。 


得到next表之后就可以依据next表写出kmp方法, 跟得到next表的方法几乎相同: 

```
func kmp(haystack string, needle string) int {
	if needle == "" {
		return 0
	}
	prefixTable := getPrefixTable(needle)
	var j int
	for i := 0; i < len(haystack); {
		if haystack[i] == needle[j] {
			// 匹配, 查下一个
			j++
			i++
		} else {
			if j > 0 {
				//不匹配, 尝试前一个prefix
				j = prefixTable[j-1]
			} else {
				//前一个为0, 无匹配, 移动i
				i++
			}
		}
		if j == len(needle) {
			//找到匹配, 返回
			return i - j + 1
		}
	}
	return -1
}

```











