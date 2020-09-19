#include <opencv2/core/core.hpp>
#include <opencv2/highgui/highgui.hpp>
#include <opencv2/imgproc.hpp>
#include <iostream>
#include <string>
#include <vector>
#include <map>
#include <set>
#include <cmath>
#include <iterator>
#include <limits>
#include "Images.h"

using namespace cv;
using namespace std;

void historgamAnalysis(uint8_t* image, const uint32_t size);
void meanAndVariance(const uint32_t size);
void classVariance(const uint8_t t, const uint32_t size);
void otsuThresholding(uint8_t* image, const uint32_t size);

static uint32_t images[260 * 194];
static uint8_t imagesGray[260 * 194];
static uint8_t imagesGrayThreshold[260 * 194];
static map<uint8_t, uint32_t> imageHistogram;
static map<uint8_t, uint32_t> class1, class2 ;
static vector<double> _findBetweenClasses;
static double _mean, _variance, _class2_weight, _class1_weight, _class2_variance, _class1_variance, _class_variance, _class1__mean, _class2__mean,_class__between;
static vector<uint8_t> _threshold_value;

int main()
{
	otsuThresholding(TestImage2, sizeof(TestImage2) / sizeof(TestImage2[0])/2);
	Mat image(194, 260, CV_8UC4, &images);
	imshow("image", image);
	Mat imageGray(194, 260, CV_8UC1, &imagesGray);
	imshow("imageGray", imageGray);
	Mat Threshold(194, 260, CV_8UC1, &imagesGrayThreshold);
	imshow("Threshold", Threshold);
	Mat thresholdGray;
	threshold(imageGray, thresholdGray,0,255, THRESH_BINARY + THRESH_OTSU);
	imshow("thresholdGrayOpencv", thresholdGray);
	waitKey(0);
}


void  otsuThresholding(uint8_t* image, const uint32_t size) {
	uint8_t* tempImage = new uint8_t[size];		
	double highBetweenClass = 0.00, lowWithinClass = numeric_limits<double>::max();
	uint32_t counter = 0;
	historgamAnalysis(image,size);
	meanAndVariance(size);
	for(size_t i = 0; i < imageHistogram.size(); i++){
		classVariance(i, size);
	}
	for (auto i : _findBetweenClasses) {
		if (lowWithinClass > i) {
			lowWithinClass = i;			
		}
	}
	auto index_threshold = find(_findBetweenClasses.begin(),_findBetweenClasses.end(), lowWithinClass);
	int index;
	if (index_threshold != _findBetweenClasses.end()) {
		index = index_threshold - _findBetweenClasses.begin();
	}
	map<uint8_t, uint32_t>::iterator it = imageHistogram.begin();
	
	for (int i = 0; i < index; i++) {
		it++;
	}
	
	for (size_t i = 0; i < size; i++) {
		if (imagesGray[i] < it->first)
			imagesGrayThreshold[i] = 0;
		else
			imagesGrayThreshold[i] = 255;
	}
	
}


void historgamAnalysis(uint8_t* image,const uint32_t size) {
	uint32_t red = 0, green = 0, blue = 0, counter = 0,  counterTemp = 0;
	uint16_t rgb565 = 0;
	
	for (size_t i = 0; i < size; i++) {
		rgb565 = *(image+ (++counterTemp));
		rgb565 <<= 8;
		rgb565 |= *(image + (--counterTemp));
		counterTemp += 2;
		red = ((((rgb565 & 0xF800) >> 11) ) << 3);
		green = ((((rgb565 & 0x7E0) >> 5) ) << 2);
		blue = (((rgb565 & 0x1F)) << 3);
		images[counter] = (red << 16) | (green << 8) | (blue);		
		imagesGray[counter++] = (red + green + blue) / 3;
	}

	for (size_t i = 0; i < size; i++) {
		imageHistogram[imagesGray[i]]++;
	}
}

void meanAndVariance(const uint32_t size) {
	uint32_t counter = 0;
	_mean = 0.00;
	_variance = 0.00;
	for (auto i : imageHistogram) {
		_mean += ((counter++) * static_cast<double>(i.second));
	}
	_mean /= size;
	counter = 0;
	for (auto i : imageHistogram) {
		_variance += (pow(((counter++) - (_mean)), 2) * (i.second));
	}
	_variance /= size;
}

void classVariance(const uint8_t t, const uint32_t size) {
	
	uint32_t counter = 0;
	double temp = 0.00;
	
	
	class1.clear();
	class2.clear();

	_class1_weight = 0.00;
	_class2_weight = 0.00;
	_class1_variance = 0.00;
	_class2_variance = 0.00;
	_class1_weight = 0.00;

	for (auto i : imageHistogram) {
		if (counter > t)
			break;
		class1[i.first]=i.second;
		counter++;
	}
	counter = 0;
	for (auto i : imageHistogram) {
		if (counter > t)
			class2[i.first]=i.second;
		counter++;
	}
	counter = 0;
	for (auto i : class1) { 
		_class1_weight += static_cast<double>((i.second) );
		temp += static_cast<double>(i.second);
		_class1__mean += ((counter++) * static_cast<double>(i.second));
	}
	_class1_weight /= size;
	_class1__mean /= temp;
	counter = 0;
	for (auto i : class1) {
		_class1_variance += (pow(((counter++) - (_class1__mean)), 2) * (i.second));
	}
	
	_class1_variance /= temp;

	temp = 0.00;
	counter = t + 1;
	for (auto i : class2) { 
		_class2_weight += static_cast<double>((i.second) );
		temp += static_cast<double>(i.second);
		_class2__mean += ((counter++) * static_cast<double>(i.second));
	}
	_class2_weight /= size;
	_class2__mean /= temp;
	counter = t+1;
	for (auto i : class2) {
		_class2_variance += (pow(((counter++) - (_class2__mean)), 2) * (i.second));
	}
	_class2_variance /= temp;
	_class_variance = (_class1_weight * _class1_variance) + (_class2_weight * _class2_variance);
	_findBetweenClasses.push_back(_class_variance);
	_threshold_value.push_back(t);
}
