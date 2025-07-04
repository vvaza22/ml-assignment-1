# ML Assignment 1 - House Prices

კონკურსში მოცემული გვაქვს სახლების მონაცემთა ბაზა სხვადასხვა feature-ებით და target SalePrice, რომელიც არის სახლის გაყიდვის ფასი. ჩვენი დავალებაა, რომ გავწვრთნათ ისეთი მოდელი, რომელსაც სახლის პარამეტრებს გადავცემთ და შეეცდება გამოიცნოს, რა ფასად გაიყიდება მოცემული სახლი.

Submission-ები ფასდება Root-Mean-Squared-Error(RMSE) ფუნქციით რეალური ფასისა და predicted ფასის ლოგარითმს შორის. როგორც პირობაში წერია ლოგარითმი განაპირობებს, რომ ცდომილებებს იაფასიანი სახლის ფასის გამოცნობისას და ძვირადღირებული ფასის გამოცნობისას ერთნაირი გავლენა ექნება საბოლოო შედეგზე. ეს ლოგიკურია, რადგან ჩვენ 100 დოლარიან და 200 დოლარიან ნივთს შორის ბუნებრივად გვგონია, რომ 100 დოლარიანი ნივთი გაცილებით უფრო იაფია. თუმცა 100,000 დოლარიან და 100,100 დოლარიან ნივთს შორის ამ 100 დოლარიან განსხვავებას დიდი მნიშვნელობა აღარ აქვს.

# ჩემი მიდგომა პრობლემის გადასაჭრელად

მონაცემების train და validation set-ად გაჭრის შემდეგ, ვაკვირდები მხოლოდ train set-ს და ვცდილობ მხოლოდ ამ სიმრავლეზე დაყრდნობით მივიღო გადაწყვეტილებები მოდელის ჰიპერპარამეტრების შესახებ.

თავიდანვე ვხვდები, რომ მხოლოდ ის feature-ები უნდა შევარჩიო, რომლებსაც კორელაცია გააჩნიათ SalePrice-თან. რადგანაც ჩვენს მონაცემებს ძალიან ბევრი feature არ აქვს და შესაძლებელია, ხელით დავაკვირდე, გადავწყვიტე, რომ ამეგო scatter plot-ები, რომელზეც დავიტანდი SalePrice vs. Feature ორ განზომილებიან სივრცეში, სადაც Feature ერთ-ერთი პარამეტრია train dataset-დან. ამ გზით შემიძლია დავაკვირდე თვალით რაიმე შესამჩნევი კორელაცია არსებობს თუ არა მონიშნულ feature-სა და SalePrice-ს შორის და პირველ რიგში მხოლოდ ის feature-ები შევარჩიო, რომელთათვისაც კორელაცია აშკარაა. მაგალითად ცხადად გამოჩნდა scatter plot-ში, რომ OverallQual წრფივად და პირდაპირპროპორციულად იზრდება ფასთან ერთად, თუმცა მსგავსი კავშირი არ შეიმჩნევა YrSold-სა და MoSold-ზე. აქ ვხდები, რომ ასეთი შერჩევის მეთოდით შეიძლება, რაღაც მნიშვნელოვანი დეტალი გამომრჩეს, რადგან მხოლოდ 2 ცვლადს ვაკვირდები. მაგალითად YrSold-მა შეიძლება სხვა დამატებით პარამეტრთან ერთად მომცეს კორელაცია. შესაძლოა, რაღაცა ტიპის სახლები 2006-2008 წლებში უფრო ძვირად იყიდებოდა და 2008 წლიდან მათი ფასი დაეცა, თუმცა სხვა ტიპის სახლების ფასმა აიწია და საბოლოოდ ისე გამოვიდა, რომ მხოლოდ YrSold-ს არ ეტყობა არანაირი დამოკიდებულება SalePrice-თან, თუმცა YrSold პირობაში სხვა ცვლადს გააჩნია კორელაცია. ამ ყველაფრის მიუხედავად, თავდაპირველი მოდელისთვის არ შევარჩევ YrSold-ს და მსგავს ცვლადებს და მხოლოდ თავდაპირველი მოდელის გაწვრთნის და შეფასების მერე ჩავუმატებ მათ, რათა დავაკვირდე მოდელის performance გაუმჯობესდება თუ არა.

ასევე შემიძლია ავაგო განაწილების გრაფიკი თითოეული feature-ისთვის. თუ ძალიან არადაბალანსებულია feature-ის მნიშვნელობები, მაგალითად Utilities feature-ში მხოლოდ 1 მონაცემს აქვს მნიშვნელობა NoSeWa და დანარჩენს სულ აქვთ მნიშვნელობა AllPub. ამ შემთხვევაში მირჩევნია, რომ ეს feature საერთოდ არ გამოვიყენო training-ში, რადგან შეიძლება მოგვცეს overfit მოდელი იმ 1 მონაცემის გამო.

ვაგებ SalePrice-ის PDF განაწილებასაც, რათა დაახლოებით მივხდე, თუ რა range-ში მხვდება ფასები training set-ში და რამხელა ვარიაციას უნდა ველოდე მოდელის პასუხებში.


# Cleaning მიდგომები

NA-ების შესავსებად გადავწყვიტე, რომ numerical feature-ებისთვის training set-დან დამეთვალა მედიანა და NA-ები შემევსო მედიანით, ხოლო categorical feature-ებისთვის დავითვალე მოდა და მოდით შევავსე NA-ები. შევქმენი NAFiller კლასი pipeline-ისთვის და fit()-ს ვაკეთებ მხოლოდ training set-ზე, რათა შემთხვევით არ მოხვდეს data leakage test set-დან.

drop feature threshold-ად ავიღე 55%, რაც იმას ნიშნავს, რომ feature-ში თუ 55%-ზე მეტი NA არის, მაშინ ამ feature-ს აღარ ვიყენებ. feature-ების დადროფვას NAFiller აკონტროლებს.

ამ დავალებაში მაქსიმალურად ვეცადე, რომ Inference-სა და Experiment-ში ერთი და იგივე კოდი არ გამემეორებინა, ამიტომაც მთლიანი Dataset გადაეცემა pipeline-ს და feature-ებს თვითონ pipeline არჩევს.


# Category encoding მიდგომები

კატეგორიათა უმრავლესობა პირდაპირ ითარგმნებოდა რიცხვებში ordinal encoding-ის საშუალებით, რაც იმას ნიშნავს რომ ბიექციური მიმართება ავაგე მსგავსი კატეგორიებისთვის. მაგალითად Quality-ის მნიშვნელობები თუ იყო ["Po", "Fa", "TA", "Gd", "Ex"], შევუსაბამე 0, 1, 2, 3, 4 და ა. შ.
მსგავსი შესაბამისობა გამოვიყენე ისეთი ცვლადებისთვისაც, რომელთვისასაც scatter plot-დან აშკარად ჩანდა ერთი მნიშვნელობა მეორეზე უარესი იყო.
მაგალითად LandSlope-ის Sev მნიშვნელობის ფასები უფრო დაბალ ფასად იყიდებოდა ვიდრე Mod ან Gtl.

რამდენიმე კატეგორიებისთვის გამოვიყენე One-Hot-Encoding, თუმცა ამ encoding-ის გამოყენების დროს ფრთხილად ვიყავი, რათა მონაცემების განზომილება ძალიან არ გამეზარდა.

# Feature Selection

დაკვირვების შედეგად ავაგე 3 list, რომლებშიც გადავანაწილე შედარებით high correlation, medium correlation და low correlation feature-ები SalePrice-თან მიმართებით. ექსპერიმენტს ვიწყებდი ჯერ high correlation list-დან და შემდეგ ვამატებდი დანარჩენ ლისტებს და ვაკვირდებოდი, როგორ იცვლებოდა შედეგი. ამით ვხდებოდი იყო თუ არა თავდაპირველი მოდელი undefitted და რა feature-ებს შეეძლო გამოეწვია overfit.

One-Hot-Encoding-ისგან მიღებულ feature-ები განსაკუთრებით სახიფათო იყო, რადგან ზოგი მნიშვნელობა იშვიათად გვხდებოდა train set-ში და იწვევდა overfit-ს მაგ 1 კონკრეტულ მნიშვნელობაზე. ამიტომაც დავხედე განაწილების ცხრილს, რომელიც დავსორტე მნიშვნელობების გამოყენებების მიხედვით კლებადობით და ამ მნიშვნელობებიდან ავარჩიე მხოლოდ პირველი რამდენიმე.

სწორი feature-ების ასარჩევად საკმაოდ ბევრი trail-and-error დამჭირდა.


# Training

ჩემი მოდელის შესაფასებლად ვიყენებდი:

1. R<sup>2</sup> მეტრიკას კორელაციის შესამოწმებლად feature-ებისა და SalePrice-ს შორის
1. Mean Absolute Error(MAE), რომელიც მიჩვენებს საშუალოდ რა ფასით ეშლება მოდელს. ეს მეტრიკა არ ითვალისწინებს ძვირადღირებული სახლის გამოცნობისას მოხვდა ეს ცდომილება თუ იაფასიანი.
1. Mean Squared Error(MSE), რომელიც უფრო დიდ ცდომილებას გაძლევს თუ აცდენა დიდია.
1. Root Mean Squared Error(RMSE), იგივე რაც MSE ოღონდ დოლოარია მისი ერთეული და არა დოლარ<sup>2</sup>
1. Root Mean Squared Log Error(RMSE_LOG), ეს არის მთავარი მეტრიკა, რომლითაც თვითონ kaggle აფასებს. დათვლილია ფასების ლოგარითმი და შემდეგ RMSE.

ექსპერიმენტები დავიწყე ჯერ ძალიან მარტივი მოდელით. ავარჩიე მხოლოდ 2 მნიშვნელოვანი feature განაწილების გრაფიკებიდან და გავწვრთენი linear model.

პირველი 9 ექსპერიმენტი ვმუშაობდი linear model-ებზე, ვცდილობდი სხვადასხვა რაოდენობის feature-ების შერჩევას და ვაკვირდებოდი, რა გავლენას იქონიებდა შედეგზე. linear model-ის შემთხვევაში აღმოჩნდა, რომ feature-ების დამატება უმეტესწილად აუმჯობესებდა შედეგს train set-ზეც და test set-ზეც. თუმცა `RMSE_TEST`-ის მნიშვნელობა `34656`-ზე უკეთ ვერ გავაუმჯობესე. განაწილებების გრაფიკებიდან ჩანდა, რომ აშკარად არ იყო ზოგიერთი feature წრფივად დამოკიდებული SalePrice-ზე და შედეგად დავასკვენი, რომ linear model ექსპერიმენტებს ჰქონდათ მაღალი bias. ვიყენებდი გაცილებით უფრო მარტივ მოდელს, ვიდრე მონაცემები მოითხოვდა.

ამ ეტაპზე მქონდა მონიშნნული უკვე საკმაოდ ბევრი feature და გადავწყვიტე, რომ შემეცვალა polynomial degree ჰიპერპარამეტრი. გავზარდე იგი 2-მდე, რამაც მაშინვე overfitted მოდელი მომცა. ძალიან კარგი შედეგი დადო train set-ზე, თუმცა გაცილებით უფრო უარესი შედეგი დადო test set-ზე. ეს იმან გამოიწვია, რომ უფრო expressive მოდელმა დეტალურად დაიმახსოვრა training set მონაცემები, თუმცა განზოგადება. ბევრი ფიქრის შემდეგ გადავწყვიტე, რომ მხოლოდ high correlated feature-ების list დამეტოვა ამ ექსპერიმენტისთვის, რამაც შედეგი გამოიღო და უკეთესი შედეგი დადო test set-ზე, ვიდრე ყველა სხვა linear model-მა. `RMSE_TEST 33618`, ხოლო `RMSE_LOG_TEST 0.19`

იმისათვის, რომ მიმეღწია უფრო უკეთეს შედეგს, ვცდილობდი, რომ ნელ-ნელა დამემატებინა მეტი feature-ები ჩემი მოდელისთვის, თუმცა medium correlated სიმრავლიდანაც კი feature-ების დამატება მკვეთრად აუარესებდა შედეგს test-ზე შედეგს. იყო შემთხვევები, როდესაც SalePrice prediction ხდებოდა უარყოფითი და შედეგად `RMSE_LOG_TEST` ვეღარ ითვლებოდა. ეს იმით იყო გამოწვეული, რომ overfit-ს აკეთებდა მნიშვნელობებზე, რომლებისთვისაც საკმარისი მონაცემები არ გვქონდა ან არ იყო კორელაცია ამ მნიშვნელობასა და SalePrice-ს შორის აშკარა. აქ გადავწყვიტე რომ გამომეყენებინა `Regularization` ტექნიკა, კონკრეტულად `Ridge`, რომელიც ხელს უშლის `theta` მნიშვნელობებს ძალიან გაიზარდოს. ამან ჩემს მოდელს დაამატა ახალი ჰიპერპარამეტრი `regularization_lambda_constant`. რაც უფრო მაღალი არის ამ ჰიპერპარამეტრის მნიშვნელობა, მით უფრო დასჯის მოდელს დიდი `theta` მნიშვნელობების შერჩევისას.


`Regularization`-ის დამატებამ უკეთესი შედეგი მომცა მოდელისთვის. `polynomial degree=3` შემთხვევაში სწორი feature-ების შერჩევის შედეგად მივაღწიე საუკეთესო `RMSE_LOG_TEST 0.197`, ხოლო `polynomial degree=2` შერჩევის შედეგად `RMSE_LOG_TEST 0.149`. `regularization_lambda_constant=5` შევარჩიე საბოლოო ექსპერიმენტში.


დაკვირვების შედეგად დავადგინე, რომ თუ `lambda` პარამეტრი ძალიან დაბალი იყო, მოდელს ჰქონდა ძალიან მაღალი ვარიაცია. იმდენად მაღალი, რომ predicted `SalePrice` ზოგჯერ უარყოფითი ჯდებოდა, ამ შემთხვევაში experiment-ებში `RMSE_LOG` metric მონიშნული მაქვს, როგორც `99999999`, რათა აღმენიშნა, რომ ეს არის ყველაზე ცუდი შედეგი, როდესაც `RMSE_LOG` ვერც კი ითვლება. მსგავსი overfitted მოდელები დავლოგე ექსპერიმენტებში `PolynomialRegression Overfit 1-3`. `regularization_lambda_constant` vs `RMSE_LOG` ის გრაფიკი ავაგე და დავამატე ამ რეპოში, როგორც `lambda_vs_rmse_log.png`


ბევრი trial-and-error-ის შემდეგ საბოლოოდ შევარჩიე მნიშვნელოვანი feature-ები, რომლებიც დავლოგე experiment-ში `PolynomialRegression Fine Tuned 3` შევჯერდი `polynomial_degree=2, regularization_lambda_constant=5` ჰიპერპარამეტრებზე. მთავარი მეტრიკა `RMSE_LOG_TEST=0.149` მივიღე. მოცემული მოდელი დავასაბმითე kaggle-ის competition-ზე. შედეგი მომცა `0.175`, რაც იმას ნიშნავს, რომ საკმაოდ კარგად განზოგადდა ჩემი მოდელი. როგორც ზემოთ ნათქვამში ვახსენე, overfitted მოდელებზე validation set-სა და train set-ზე იყო უზარმაზარი აცდენა ხოლმე, იმდენად დიდი, რომ predicted price უარყოფითი გამოდიოდა, ამ შემთხვევაში კი ვფიქრობ საკმაოდ კარგი შედეგი დავდე საბოლოო test set-ზე ჩემი მოდელით.


საბოლოოდ შერჩეული მოდელის run_id არის `runs:/eb26b4ee7bb04117a3b4b35e8f9f89d2/PolynomialRegression`

# რეპოზიტორის სტრუქტურა

* model_experiment.ipynb - ჩაწერილია ყველა ნაბიჯი, რომელიც cleaning, feature selection, training, logging-ის დროს გავიარე, აგებულია განაწილების გრაფიკები და შეიცავს Pipeline-ის ლოგიკას.

* model_inference.ipynb - mlflow-დან მომაქვს საბოლოოდ შერჩეული მოდელი და ვაგზავნი kaggle competition-ზე.

* README.md - მთლიანი პროცესის სიტყვიერი აღწერა.

* lambda_vs_rmse_log.png - გვიჩვენებს Regularization Lambda Constant(Ridge)-ის დამოკიდებულებას RMSE_LOG score-ზე საბოლოო მოდელში.
