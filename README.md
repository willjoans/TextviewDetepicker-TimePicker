# TextviewDetepicker-TimePicker

import Foundation
import UIKit
import IQKeyboardManagerSwift

@IBDesignable
class DesignableUITextField: UITextField {
    /*
     required init(coder aDecoder: NSCoder) {
     super.init(coder: aDecoder)!
     initialSettings()
     }
     */
    
    var picker = UIPickerView()
    var pickerArrayData = [String]()
    var pickerDidSelect : ((Int) -> Void)?
    
    var datePicker = UIDatePicker()
    var strDateFormat = String()
    var datePickerDidSelect : ((Date) -> Void)?
    
    
    func initialSettings(){
        self.borderStyle = .none
        //self.backgroundColor = COLOR_TEXTFIELD_BG
        self.layer.cornerRadius = 5.0
        self.layer.masksToBounds = true
        self.autocorrectionType = .no
    }
    
    @IBInspectable var setBGColor : Bool = true {
        didSet {
            if setBGColor {
                initialSettings()
            }
        }
    }
    
    // Provides left padding for images
    override func leftViewRect(forBounds bounds: CGRect) -> CGRect {
        var textRect = super.leftViewRect(forBounds: bounds)
        textRect.origin.x += leftPadding
        return textRect
    }
    
    override func rightViewRect(forBounds bounds: CGRect) -> CGRect {
        let textRect = super.rightViewRect(forBounds: bounds)
        
        return textRect
    }
    
    @IBInspectable var leftImage: UIImage? {
        didSet {
            updateView()
        }
    }
    
    
    @IBInspectable var leftPadding: CGFloat = 0 {
        didSet {
            updateView()
        }
    }
    
    @IBInspectable var rightImage: UIImage? {
        didSet {
            updateView()
        }
    }
    
    @IBInspectable var rightPadding: CGFloat = 0 {
        didSet {
            updateView()
        }
    }
    
    //    @IBInspectable var tintColor: UIColor = UIColor.lightGray {
    //        didSet {
    //            updateView()
    //        }
    //    }
    
    func updateView() {
        
        if let image = leftImage {
            leftViewMode = UITextFieldViewMode.always
            let imageView = UIImageView(image: image)
            imageView.image = image
            imageView.contentMode = .center
            //            imageView.frame = CGRect(x: 0, y: 0, width: imageView.frame.size.width + 10.0, height: self.frame.size.height)
            imageView.frame = CGRect(x: 0, y: 0, width: self.frame.size.height, height: self.frame.size.height)
            imageView.backgroundColor = UIColor.clear
            // Note: In order for your image to use the tint color, you have to select the image in the Assets.xcassets and change the "Render As" property to "Template Image".
            //            imageView.tintColor = color
            leftView = imageView
        }
        else if leftPadding == 0 {
            leftViewMode = UITextFieldViewMode.never
            leftView = nil
        }
        else if leftPadding != 0 {
            leftViewMode = UITextFieldViewMode.always
            //            let newLeftView = UIView(frame: CGRect(x: 0, y: 0, width: leftPadding, height: self.frame.size.height))
            let newLeftView = UIView(frame: CGRect(x: 0, y: 0, width: 0, height: 0))
            newLeftView.backgroundColor = UIColor.clear
            newLeftView.isUserInteractionEnabled = false
            leftView = newLeftView
        }
        
        
        if let image = rightImage {
            rightViewMode = UITextFieldViewMode.always
            let imageView = UIImageView(image: image)
            imageView.image = image
            imageView.contentMode = .center
            //            imageView.frame = CGRect(x: 0, y: 0, width: imageView.frame.size.width + 10.0 , height: self.frame.size.height)
            imageView.frame = CGRect(x: 0, y: 0, width: self.frame.size.height , height: self.frame.size.height)
            imageView.backgroundColor = UIColor.clear
            //            rightView = imageView
            
            let newRightView = UIView(frame: CGRect(x: 0, y: 0, width: rightPadding + imageView.frame.size.width, height: self.frame.size.height))
            imageView.frame = CGRect(x: newRightView.frame.size.width - imageView.frame.size.width, y: 0, width: self.frame.size.height , height: self.frame.size.height)
            newRightView.addSubview(imageView)
            newRightView.backgroundColor = UIColor.clear
            newRightView.isUserInteractionEnabled = false
            rightView = newRightView
        }
        else if rightPadding == 0 {
            rightViewMode = UITextFieldViewMode.never
            rightView = nil
        }
        else if rightPadding != 0 {
            rightViewMode = UITextFieldViewMode.always
            let newRightView = UIView(frame: CGRect(x: 0, y: 0, width: rightPadding, height: self.frame.size.height))
            //            let newRightView = UIView(frame: CGRect(x: 0, y: 0, width: 0, height: 0))
            newRightView.backgroundColor = UIColor.clear
            newRightView.isUserInteractionEnabled = false
            rightView = newRightView
        }
        
        // Placeholder text color
        //        attributedPlaceholder = NSAttributedString(string: placeholder != nil ?  placeholder! : "", attributes:[NSForegroundColorAttributeName: color])
        
    }
}



//NOTE:-
// BELOW is for Picker View
extension DesignableUITextField : UIPickerViewDataSource, UIPickerViewDelegate {
    
    func addPickerView(_ arrData : [String] ,  callback: ((Int) -> Void)?) {
        pickerArrayData = arrData
        pickerDidSelect = callback
        picker = UIPickerView()
        picker.dataSource = self
        picker.delegate = self
        picker.reloadAllComponents()
        self.inputView = picker
    }
    
    func numberOfComponents(in pickerView: UIPickerView) -> Int {
        return 1
    }
    
    func pickerView(_ pickerView: UIPickerView, numberOfRowsInComponent component: Int) -> Int {
        return pickerArrayData.count
    }
    
    func pickerView(_ pickerView: UIPickerView, titleForRow row: Int, forComponent component: Int) -> String? {
        return pickerArrayData[row]
    }
    
    func pickerView(_ pickerView: UIPickerView, didSelectRow row: Int, inComponent component: Int) {
        //        print("\n************* didSelectRow **********************************\n")
        self.text = pickerArrayData[row]
        if pickerDidSelect != nil {
            pickerDidSelect!(row)
        }
    }
}


extension DesignableUITextField {
    func addDatePicker(mode : UIDatePickerMode, dateFormat : String, callback: ((Date) -> Void)?) {
        datePickerDidSelect = callback
    
        strDateFormat = dateFormat
        datePicker = UIDatePicker()
        datePicker.datePickerMode = UIDatePickerMode.date
        datePicker.minimumDate = nil
        datePicker.maximumDate = Date()
      
        self.inputView = datePicker
        
        datePicker.addTarget(self, action: #selector(dateChanged(_:)), for: .valueChanged)
    }
    
    @objc func dateChanged(_ sender: UIDatePicker){
        //For date formate
        let formatter = DateFormatter()
        formatter.dateFormat = strDateFormat
        self.text = formatter.string(from: datePicker.date)
        if datePickerDidSelect != nil {
            datePickerDidSelect!(datePicker.date)
        }
    }
    
    
}


extension DesignableUITextField {
    func addDatePickerTime(mode : UIDatePicker.Mode, dateFormat : String, callback: ((Date) -> Void)?) {
        datePickerDidSelect = callback
        
        strDateFormat = dateFormat
        datePicker = UIDatePicker()
        datePicker.datePickerMode = UIDatePicker.Mode.time
       // datePicker.maximumDate = Date()
        self.inputView = datePicker
        
        datePicker.addTarget(self, action: #selector(dateChangedTime(_:)), for: .valueChanged)
    }
    
    @objc func dateChangedTime(_ sender: UIDatePicker){
        //For date formate
        let formatter = DateFormatter()
        formatter.dateFormat = strDateFormat
        self.text = formatter.string(from: datePicker.date)
        if datePickerDidSelect != nil {
            datePickerDidSelect!(datePicker.date)
        }
    }
}
extension DesignableUITextField {
    func addDatePickerAssignMent(mode : UIDatePickerMode, dateFormat : String, callback: ((Date) -> Void)?) {
        datePickerDidSelect = callback
        
        strDateFormat = dateFormat
        datePicker = UIDatePicker()
        datePicker.datePickerMode = UIDatePickerMode.date
        datePicker.maximumDate = Date()
        
        
        self.inputView = datePicker
        
        datePicker.addTarget(self, action: #selector(dateChangedAssign(_:)), for: .valueChanged)
    }
    
    @objc func dateChangedAssign(_ sender: UIDatePicker){
        //For date formate
        let formatter = DateFormatter()
        formatter.dateFormat = strDateFormat
        self.text = formatter.string(from: datePicker.date)
        if datePickerDidSelect != nil {
            datePickerDidSelect!(datePicker.date)
        }
    }
    
    
}
extension DesignableUITextField {
    func addDatePickerHomeWork(mode : UIDatePickerMode, dateFormat : String, callback: ((Date) -> Void)?) {
        datePickerDidSelect = callback
        
        strDateFormat = dateFormat
        datePicker = UIDatePicker()
        datePicker.datePickerMode = UIDatePickerMode.date
       
        self.inputView = datePicker
        
        datePicker.addTarget(self, action: #selector(dateChangedHomeWork(_:)), for: .valueChanged)
    }
    
    @objc func dateChangedHomeWork(_ sender: UIDatePicker){
        //For date formate
        let formatter = DateFormatter()
        formatter.dateFormat = strDateFormat
        self.text = formatter.string(from: datePicker.date)
        if datePickerDidSelect != nil {
            datePickerDidSelect!(datePicker.date)
        }
    }
    
    
}
extension DesignableUITextField {
    func addDatePickerAttendance(mode : UIDatePickerMode, dateFormat : String, callback: ((Date) -> Void)?) {
        datePickerDidSelect = callback
        
        strDateFormat = dateFormat
        datePicker = UIDatePicker()
        datePicker.datePickerMode = UIDatePickerMode.date
        datePicker.maximumDate = Date()
        self.inputView = datePicker
        
        datePicker.addTarget(self, action: #selector(dateChangedHomeWorkAttendance(_:)), for: .valueChanged)
    }
    
    @objc func dateChangedHomeWorkAttendance(_ sender: UIDatePicker){
        //For date formate
        let formatter = DateFormatter()
        formatter.dateFormat = strDateFormat
        self.text = formatter.string(from: datePicker.date)
        if datePickerDidSelect != nil {
            datePickerDidSelect!(datePicker.date)
        }
    }
    
    
}
extension DesignableUITextField {
    func addDatePickerAssignMenAllott(mode : UIDatePickerMode, dateFormat : String, callback: ((Date) -> Void)?) {
        datePickerDidSelect = callback
        
        strDateFormat = dateFormat
        datePicker = UIDatePicker()
        datePicker.datePickerMode = UIDatePickerMode.date
        datePicker.minimumDate = Date()
        
        
        self.inputView = datePicker
        
        datePicker.addTarget(self, action: #selector(dateChangedAssignnAllott(_:)), for: .valueChanged)
    }
    
    @objc func dateChangedAssignnAllott(_ sender: UIDatePicker){
        //For date formate
        let formatter = DateFormatter()
        formatter.dateFormat = strDateFormat
        self.text = formatter.string(from: datePicker.date)
        if datePickerDidSelect != nil {
            datePickerDidSelect!(datePicker.date)
        }
    }
}

        let date = Date()
        formatter.dateFormat = "dd/MM/yyyy"
        let ToDayDate = formatter.string(from: date)
        self.txtstartdate.text = ToDayDate

        txtstartdate.addDatePickerAttendance(mode: .date, dateFormat: "yyyy-MM-dd") { (resultDate : Date) in
            
            self.DateStr = resultDate
            self.formatter.dateFormat = "yyyy-MM-dd"
            self.StartDate = self.formatter.string(from: self.DateStr)
            
        }
        
        self.txtTime.addDatePickerTime(mode: .date, dateFormat: "HH:mm:ss") { (resultDate : Date) in
            print(resultDate)
        }
        let date = Date()
        let formatter = DateFormatter()
        formatter.dateFormat = "ddMMyyyyhhss"
        strImagename = formatter.string(from: date)
    }
    
      let invocations = IQInvocation(self, #selector(didPressOnDoneButtonTIME))
        txtTime.keyboardToolbar.doneBarButton.invocation = invocations
        
         @objc func didPressOnDoneButtonTIME() {
        self.view.endEditing(true)
        if txtTime.text == ""{
            let date = Date()
            let formatter = DateFormatter()
            formatter.dateFormat = "HH:mm:ss"
            let result = formatter.string(from: date)
            self.txtTime.text = result
            //            self.SelectedDate = result
        }
        
    }
        
        
        
    
        
        




