[TOC]
# C++ Code Snippet

## 找到重复的元素并剔除

1. 需要元素是有序元素
2. 剔除后需要重新设定大小，删除剔除的元素

```cpp
template<typename T>
class Edge {
public:
	int ind0_, ind1_;
	std::vector<AuxTriangle<T>> adj_triangles_;

	Edge() = default;
	//总是把小的序号放前面
	Edge(int ind0, int ind1) :ind0_(ind0), ind1_(ind1) {
		if (ind1_ < ind0_) {
			ind0_ = ind1_;
			ind1_ = ind0;
		}
	}
};

//用于排序
auto les_func = [](const Edge<T>& e0,const Edge<T>& e1)
	{
		if (e0.ind0_ < e1.ind0_) return true;
		else if (e0.ind0_ > e1.ind0_) return false;
		else if (e0.ind0_ == e1.ind0_) {
			if (e0.ind1_ < e1.ind1_) return true;
			else if (e0.ind1_ > e1.ind1_) return false;
			else if (e0.ind1_ == e1.ind1_) return false;
		}
	};
//用于判断是否相等	
auto eql_func = [](const Edge<T>& e0, const Edge<T>& e1)
{
	return ((e0.ind0_ == e1.ind0_) && (e0.ind1_ == e1.ind1_))||
		((e0.ind0_ == e1.ind1_) && (e0.ind1_ == e1.ind0_));
};

for (int i = 0; i < position_indices.size(); i += 3) {
		int ind0 = position_indices[i];
		int ind1 = position_indices[i + 1];
		int ind2 = position_indices[i + 2];

		//triangle list
		triangles.push_back(AuxTriangle<T>(ind0, ind1, ind2));
		//MUST BE INITLIZATED BEFORE USING
		Edge<T> edge0(ind0, ind1), edge1(ind0, ind2), edge2(ind1, ind2);
		//edge list，里面可能有重复边(0-1,1-0等价)
		edges.push_back(edge0);
		edges.push_back(edge1);
		edges.push_back(edge2);
	}
	//先排序，后剔除
	std::sort(edges.begin(), edges.end(), les_func);
	auto ip = std::unique(edges.begin(), edges.end(), eql_func);
	//一定要resize
	edges.resize(std::distance(edges.begin(), ip));
	std::cout << "edges: " << edges.size() << "\n";
```

##使用boost方便的获得年月日时分秒

```cpp
//使用获得的时间信息创建Log文件
#include<iostream>
#include<fstream>
#include<ctime>
#include<string>

#include<boost/format.hpp>
#include<boost/filesystem.hpp>
#include<boost/date_time/gregorian/gregorian.hpp>
#include<boost/date_time/posix_time/posix_time.hpp>

int main()
{
	std::string file_dir = "../logs/";
	boost::filesystem::path log_dir(file_dir);
	if(!boost::filesystem::exists(log_dir)){
		boost::filesystem::create_directory(file_dir);
	}

	//year,mouth and day
	std::string log_ymd = boost::gregorian::to_iso_string(boost::gregorian::day_clock::local_day());
	//hour minute and second
	std::string log_hms = boost::posix_time::to_iso_string(boost::posix_time::second_clock::local_time().time_of_day());
	
	// std::cout<<log_ymd<<"\n";
	// std::cout<<log_hms<<"\n";
	std::string log_file = file_dir + "log_"+ log_ymd + log_hms;
	std::fstream log_ofs;
	log_ofs.open(log_file,std::ios::app);
	if(!log_ofs.is_open()){
		exit(0);
	}
	log_ofs.close();
	return 0;
}
```